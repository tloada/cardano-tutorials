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
2. Now let us create our _stake key pair_ :

		cardano-cli shelley stake-address key-gen \
		--verification-key-file stake.vkey \
		--signing-key-file stake.skey

It's content look like this:

    cat stake.vkey

    type: StakingVerificationKeyShelley
    title: Free form text
    cbor-hex:
    18b958203e...

### Payment address
3. We then use `payment.vkey` and `stake.vkey` to create our `payment address`:

		cardano-cli shelley address build \
		--payment-verification-key-file payment.vkey \
		--stake-verification-key-file stake.vkey \
		--out-file payment.addr \
		--testnet-magic 42

This created the file payment.addr that is already associated with our stake keys:

    cat payment.addr
    > 00ec78e3d3916636101f6d9539c451f248ba200f38f2c33129f7ef36d66853603e872296956a4d86

4. To query your address (see the utxo's at that address),
   you first need to set environment variable `CARDANO_NODE_SOCKET_PATH`
   to the socket-path specified in your node configuration. In this example we will use
   the block-producing node created in the previous steps:

        export CARDANO_NODE_SOCKET_PATH=~/cardano-node/relay/db/node.socket

   and make sure that your node is running.  Then use

       cardano-cli shelley query utxo \
       --address $(cat payment.addr) \
       --testnet-magic 42

   you should see something like this:

                              TxHash                                 TxIx        Lovelace
    ----------------------------------------------------------------------------------------

   (The `--testnet-magic 42` is specific to the Shelley Testnet, for mainnet we would use `--mainnet` instead.)


### Stake address
5. Finally, we can create our stake address. This address __CAN'T__ receive payments but will receive the rewards from participating in the protocol. We will save this address in the file `stake.addr`

		cardano-cli shelley stake-address build \
		--stake-verification-key-file stake.vkey \
		--out-file stake.addr \
		--testnet-magic 42

This created the file stake.addr, let's check its content:

    cat stake.addr
    > 5821e0872296956a4d86ee9654060734e83dddc56016fb2ecc7cbb435ee8e3c1053d9d
