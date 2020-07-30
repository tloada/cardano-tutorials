# Creando llaves y direcciones

ACTUALIZADO PARA _TAG_: 1.14.2

Necesitamos crear dos sets de llaves (_**keys**_) y direcciones (_**addresses**_): Un set para manejar nuestros fondos (hacer y recibir pagos) y un set para manejar nuestra _**stake**_ (participación), necesaria para participar en el protocolo de delegación de nuestra _**stake**_.

Primeramente, vamos a producir nuestras llaves criptográficas, ya que las necesitaremos más a delante para crear nuestras direccione:

### Par de llaves de pago (payment key pair)
1. Primero necesitamos generar nuestro par de llaves de pago:

        cardano-cli shelley address key-gen \
            --verification-key-file payment.vkey \
            --signing-key-file payment.skey

   Esto creará dos directorios (nosotros los llamaremos `payment.vkey` and `payment.skey`),
   uno conteniendo la _**public verification key**_ (llave pública de verificación), otro la _**private signing key**_ (llave privada para firmar).

   Los archivos están en formato de texto simple y legible para humanos:

        cat payment.vkey

        > type: VerificationKeyShelley
        > title: Free form text
        > cbor-hex:
        >  18af58...

   La primera línea describe el tipo de archivo y no debería de ser cambiado.
   La segunda línea es forma de texto libre (_free form text_) la cual podemos cambiar si así lo deseamos.
   La llave en sí es la cadena de _bytes_ cifrada en _cbor_ en la cuarta línea.

### Par de llaves de participación (stake key pair)
2. Ahora crearemos nuestro par de llaves de participación:

		cardano-cli shelley stake-address key-gen \
		--verification-key-file stake.vkey \
		--signing-key-file stake.skey

   El contenido de las llaves será parecido a este:

    	cat stake.vkey

    	type: StakingVerificationKeyShelley
    	title: Free form text
    	cbor-hex:
    	18b958203e...

### Dirección de pago (payment address)
3. Luego usamos la `payment.vkey` y `stake.vkey` para crear nuestra `payment address`:

		cardano-cli shelley address build \
		--payment-verification-key-file payment.vkey \
		--stake-verification-key-file stake.vkey \
		--out-file payment.addr \
		--testnet-magic 42

   Esto creó el archivo payment.addr que ya está asociado con nuestras _stake keys_:

    	cat payment.addr
    	> 00ec78e3d3916636101f6d9539c451f248ba200f38f2c33129f7ef36d66853603e872296956a4d86

4. Para consultar tu dirección (ver los _UTXOs_ en esa dirección), primero necesitás fijar la variable de ambiente `CARDANO_NODE_SOCKET_PATH`
   hacia la ruta de tu _socket_ en tu configuración del nodo. En este ejemplo usamos el nodo productor de bloques creado en los pasos anteriores:

        export CARDANO_NODE_SOCKET_PATH=~/cardano-node/relay/db/node.socket

   y asegúrate que el nodo actualmente está siendo ejecutado. Luego usá

       cardano-cli shelley query utxo \
       --address $(cat payment.addr) \
       --testnet-magic 42

   Y deberías de ver algo como esto:

                              TxHash                                 TxIx        Lovelace
    ----------------------------------------------------------------------------------------

   (La `--testnet-magic 42` es específica de la Shelley Testnet, para la mainnet usaríamos `--mainnet` en vez.)


### Dirección de participación (stake address)
5. Finalmente, podemos crear nuestra dirección de participación. Esta dirección **NO PUEDE** recibir pagos, pero recibirá las recompensas (_rewards_) de la participación en el protocolo. Guardaremos esta dirección en el archivo `stake.addr`

		cardano-cli shelley stake-address build \
		--stake-verification-key-file stake.vkey \
		--out-file stake.addr \
		--testnet-magic 42

   Esto creó el archivo stake.addr, ahora revisemos sus contenidos:

    	cat stake.addr
    	> 5821e0872296956a4d86ee9654060734e83dddc56016fb2ecc7cbb435ee8e3c1053d9d
