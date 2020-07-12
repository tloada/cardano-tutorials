# Crear tus llaves de participación de tu pool (_stake pool keys_)

Un stake pool necesita como mínimo dos nodos ejecutándose de manera simultánea: un nodo **productor de bloques** y un nodo **de relevo**.

Necesitamos preparar nuestro nodo **productor de bloques**. Podés construir el nodo del código fuente o mantener un armado individual en tu máquina local y solamente subir los binarios a tus servidores **productor de bloques** y **de relevo**. Solamente asegúrate de que tengás versiones coherentes entre ellos.



![network diagram](images/basic-network-with-relays-producers-passivenodes-walletnodes.png)

El nodo **productor de bloques** solamente se conectará a su **relevo**, mientras que el **relevo** establecerá conexiones con otros relevos en la red. Cada nodo debe ejecutarse en un servidor independiente.

### Configuración básica del firewall del nodo productor de bloques:

* Asegurate que hayás ingresado con tus llaves SSH, no con contraseña
* Asegurate que hayás preparado conexiones SSH en un puerto distinto al 22 por default
* Asegurate que hayás configurado el firewall para solamente permitir conexiones de tus nodos de relevo mediante sus direcciones de ip.

### Basic relay node firewall configuration:

 * Asegurate que hayás ingresado con tus llaves SSH, no con contraseña.
 * Asegurate que hayás preparado conexiones SSH en un puerto distinto al 22 por default.
 * Asegurate que solamente tengás abierto los puertos necesarios.

### Creando llaves para nuestro nodo productor de bloques

**ATENCIÓN:** Vas a querer usar tu **máquina local** para este proceso (asumiendo que tengás _cardano-node_ y _cardano-cli_ instalado en ella). Asegúrate de que no estés en líneas hasta que hayás puesto tus _**cold keys (llaves frías)**_ en una memoria usb segura (encriptada) y borrado los archivos (llaves heladas) de tu máquina local.

El **nodo productor de bloques** o **pool node** necesita:

* **Cold** Key pair (par de llaves **frías**),
* **VRF** Key pair (par de llaves **VRF**),
* **KES** Key pair (par de llaves **KES**),
* **Operational Certificate** (Certificado Funcional)

Creá un directorio en tu máquina local para guardar tus llaves:

    mkdir pool-keys
    cd pool-keys

### 1. Crear llaves frías y un _cold_counter_ (contador_frío):

    cardano-cli shelley node key-gen \
    --cold-verification-key-file cold.vkey \
    --cold-signing-key-file cold.skey \
    --operational-certificate-issue-counter-file cold.counter

### 2. Generate VRF Key pair

    cardano-cli shelley node key-gen-VRF \
    --verification-key-file vrf.vkey \
    --signing-key-file vrf.skey

### 3. Generate the KES Key pair

    cardano-cli shelley node key-gen-KES \
    --verification-key-file kes.vkey \
    --signing-key-file kes.skey

### 4. Generate the Operational Certificate

We need to know the slots per KES period, we get it from the genesis file:

    cat shelley_testnet-genesis.json | grep KESPeriod
    > "slotsPerKESPeriod": 3600,

So one period lasts 3600 slots.

Then we need the current tip of the blockchain:

We can use your relay node to query the tip:

    cardano-cli shelley query tip --testnet-magic 42
    > Tip (SlotNo {unSlotNo = 432571}) ...

Look for Tip `unSlotNo` value. In this example we are on slot 432571. So we have KES period is 120:

    expr 432571 / 3600
    > 120

To generate the certificate:

    cardano-cli shelley node issue-op-cert \
    --kes-verification-key-file kes.vkey \
    --cold-signing-key-file cold.skey \
    --operational-certificate-issue-counter cold.counter \
    --kes-period 120 \
    --out-file node.cert

### Move the cold keys to secure storage and remove them from your local machine.

The best place for your cold keys is a __SECURE USB__ or other __SECURE EXTERNAL DEVICE__, not a computer with internet access.

### Copy the files to the server:

Copy your VRF keys, KES Keys, and Operational Certificate to your __block-producing__ server. For example:

    scp -rv -P<SSH PORT> -i ~/.ssh/<SSH_PRIVATE_KEY> ~/pool-keys USER@<PUBLIC_IP>:~/

    > Transferred: sent 3220, received 6012 bytes, in 1.2 seconds
    Bytes per second: sent 2606.6, received 4866.8
    debug1: Exit status 0


Log in to your server and verify that the files are there:

    ls pool-keys

    > kes.skey  kes.vkey  node.cert  vrf.skey  vrf.vkey  

Later on we will learn how to register our pool in the blockchain.
