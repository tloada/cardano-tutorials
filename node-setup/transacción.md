# Crear una simple transacción

Vas a necesitar una nueva dirección de pago (_payment2.addr_) para que puedas enviar fondos a ella. Primero, creá un nuevo par de llaves de pago para la nueva dirección:

    cardano-cli shelley address key-gen \
    --verification-key-file payment2.vkey \
    --signing-key-file payment2.skey

Esto ha creado dos llaves nuevas: __payment2.vkey__ y __payment2.skey__

Para crear __payment2.addr__ podemos usar el mismo par de stake keys que ya tenemos creado:

    cardano-cli shelley address build \
    --payment-verification-key-file payment2.vkey \
    --stake-verification-key-file stake.vkey \
    --out-file payment2.addr \
    --testnet-magic 42

Enviemos 100 ADA desde `payment.addr` hacia `payment2.addr`

Crear una transacción es un proceso que requiere varios pasos:

* Obtener los parámetros del protocolo
* Definir el _TTL (time to live)_ (tiempo para en vivo) de la transacción
* Calcular los costos
* Construir la transacción
* Firmar la transacción
* Presentar la transacción

### Obtener los parámetros del protocolo
Conseguí los parámetros del protocolo y guardalos en `protocol.json` usando:

       cardano-cli shelley query protocol-parameters \
       --testnet-magic 42 \
       --out-file protocol.json

### Definir el _TTL_ de la transacción

Necesitamos el valor ACTUAL del tip de la blockchain, esto es, la altura del último bloque producido. Estamos buscando el valor de `unSlotNo`

    cardano-cli shelley query tip --testnet-magic 42

    > Tip (SlotNo {unSlotNo = 795346}) (ShelleyHash {unShelleyHash = HashHeader {unHashHeader =        6a28a1c9fac321d7f4c8df8de68ee17f1967695460b5b422c93e6faaeeaf5cf2}}) (BlockNo {unBlockNo = 33088})

En este momento el _tip_ está en el bloque 795346.

Para construir la transacción necesitamos especificar el _**TTL (time to live)**_, esto es la altura límite del bloque para nuestra transacción a ser incluida en el bloque, si no se encuentra en un bloque para cuando llegue ese slot la transacción será cancelada.

Del archivo `protocol.json` sabemos que tenemos un _slot_ por segundo. Digamos que nos tomará 10 minutos construir la transacción, y que queremos darnos otros 10 minutos para ser incluidos en un bloque. Entonces necesitamos 20 minutos o 1200 _slots_. Por lo cual añadimos 1200 al _tip_ actual: 795346 + 1200 = 796546. Así nuestro _TTL_ es 796546.

### Calcular el costo
La transacción necesita de un (1) input: una UTXO de `payment.addr`, y dos (2) outputs: La dirección receptora **payment2.addr** y una dirección para enviar el cambio, en este caso usamos **payment.addr**

       cardano-cli shelley transaction calculate-min-fee \
       --tx-in-count 1 \
       --tx-out-count 2 \
       --ttl 796546 \
       --testnet-magic 42 \
       --signing-key-file payment.skey \
       --protocol-params-file protocol.json

       > 167965

(La `--testnet-magic 42` identifica la Shelley Testnet. Otras testnets usarán otros números, y la mainnet usa `--mainnet` en vez.)

Entonces necesitamos pagar el costo de **167965 lovelace** para crear esta transacción.

Asumiendo que queremos enviar 100 ADA a **payment2.addr** gastando una **UTXO** que contiene 1,000,000 ADA (1,000,000,000,000 lovelace), ahora necesitamos calcular cuánto es el **cambio** que debemos regresar a **payment.addr**.

    expr 1000000000000 - 100000000 - 167965

    > 999899832035

Necesitamos el _hash_ de la transacción y el _index_ (índice) de la **UTXO** que queremos gastar:

    cardano-cli shelley query utxo \
        --address $(cat payment.addr) \
        --testnet-magic 42

    >                            TxHash                                 TxIx        Lovelace
    > ----------------------------------------------------------------------------------------
    > 4e3a6e7fdcb0d0efa17bf79c13aed2b4cb9baf37fb1aa2e39553d5bd720c5c99     4     1000000000000


### Construir la transacción

Escribimos la transacción en un archivo, la llamaremos `tx.raw`.

Notá que para `--tx-in` usamos la siguiente sintaxis: `TxId#TxIx` donde `TxId` es el _hash_ de la transacción y `TxIx` es el índice y para `--tx-out` usamos: `TxOut+Lovelace` donde `TxOut` es la dirección _hex_ (hexagonal) encriptada seguida por la cantidad en `Lovelace`.

    cardano-cli shelley transaction build-raw \
    --tx-in 4e3a6e7fdcb0d0efa17bf79c13aed2b4cb9baf37fb1aa2e39553d5bd720c5c99#4 \
    --tx-out $(cat payment2.addr)+100000000 \
    --tx-out $(cat payment.addr)+999899832035 \
    --ttl 796546 \
    --fee 167965 \
    --out-file tx.raw

### Firmar la transacción
Firmá la transacción con la llave para firmar _**payment.skey**_ y guardá la transacción firmada en _**tx.signed**_

    cardano-cli shelley transaction sign \
    --tx-body-file tx.raw \
    --signing-key-file payment.skey \
    --testnet-magic 42 \
    --out-file tx.signed

### Submit the transaction
Asegurate que tu nodo sete ejecutándose y prepará la variable CARDANO_NODE_SOCKET_PATH a:

    export CARDANO_NODE_SOCKET_PATH=~/cardano-node/relay/db/node.socket

y presentá la transacción:

    cardano-cli shelley transaction submit \
            --tx-file tx.signed \
            --testnet-magic 42

### Chequear el saldo

Debemos de darle algo de tiempo para que se incorpore a la blockchain, pero eventualmente, veremos el efecto:

        cardano-cli shelley query utxo \
            --address $(cat payment.addr) \
            --testnet-magic 42

        >                            TxHash                                 TxIx        Lovelace
        > ----------------------------------------------------------------------------------------
        > b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee     1      999899832035

        cardano-cli shelley query utxo \
            --address $(cat payment2.addr) \
            --testnet-magic 42

        >                            TxHash                                 TxIx        Lovelace
        > ----------------------------------------------------------------------------------------
        > b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee     0         100000000
