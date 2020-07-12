
# Registrar la dirección de participación en la blockchain

Anteriormente, creamos nuestras llaves y direcciones, las cuales usamos para manejar nuestros fondos; también creamos nuestras llaves de participación y dirección de participación, las cuales nos permiten participar en el protocolo delegando nuestra participación o creando nuestro _**stake pool**_ (pool de participación).  

Necesitamos _registrar_ nuestra _**stake key**_ (dirección de participación) en la blockchain. Para hacerlos, debemos:

* Crear un certificado de registro
* Determinar el _**Time-to-Live (TTL)**_ (tiempo para en vivo)
* Calcular los costos y el depósito   
* Presentar el certificado a la blockchain con una transacción


### Crear un _**registration certificate**_ (certificado de registro):

    cardano-cli shelley stake-address registration-certificate \
    --stake-verification-key-file stake.vkey \
    --out-file stake.cert

Una vez que el certificado haya sido creado, debemos de incluirlo en una transacción para postearlo en la blockchain.

### Determinar el TTL
Como hicimos anteriormente, para crear una transacción necesitás determinar el _TTL_ consultando el _tip_ y agregando algunos _slots_ (espacios) para darte tiempo suficiente de construir la transacción.

### Calcular los costos y el depósito

Esta transacción consta solamente de un _input_ (la UTXO usada para pagar el costo de la transacción) y un _output_ (nuestra payment.addr a la cual enviaremos el cambio). Esta transacción debe de ser firmada por ambas la llave de firmar `payment.skey` y la llave de participación `stake.skey`; e incluye el certificado `stake.cert`:

    cardano-cli shelley transaction calculate-min-fee \
    --tx-in-count 1 \
    --tx-out-count 1 \
    --ttl 987654 \
    --testnet-magic 42 \
    --signing-key-file payment.skey \
    --signing-key-file stake.skey \
    --certificate-file stake.cert \
    --protocol-params-file protocol.json

    > 171485

En esta transacción no solamente debemos de pagar los costos de la transacción, pero también debemos de incluir un _depósito_ (el cual recuperaremos cuando cancelemos el registro de la llave) como es indicado en los parámetros del protocolo:

Podemos encontrar el monto del depósito en el archivo `protocol.json` en la opción `keyDeposit`:

        ...
        "keyDeposit": 400000,
        ...

Consultá el UTXO:

        cardano-cli shelley query utxo \
            --address $(cat payment.addr) \
            --testnet-magic 42

        >                            TxHash                                 TxIx        Lovelace
        > ----------------------------------------------------------------------------------------
        > b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee     1      1000000000

Entonces tenemos 1000 ADA, calculamos el cambio a enviar al `payment.addr`:

    expr 1000000000 - 171485 - 400000

    > 999428515

### Presentar el certificado con una transacción:

Construir la transacción:

    cardano-cli shelley transaction build-raw \
    --tx-in b64ae44e1195b04663ab863b62337e626c65b0c9855a9fbb9ef4458f81a6f5ee#1 \
    --tx-out $(cat payment.addr)+999428515 \
    --ttl 987654 \
    --fee 171485 \
    --out-file tx.raw \
    --certificate-file stake.cert

Firmarla:

    cardano-cli shelley transaction sign \
    --tx-body-file tx.raw \
    --signing-key-file payment.skey \
    --signing-key-file stake.skey \
    --testnet-magic 42 \
    --out-file tx.signed

Y presentarla:

    cardano-cli shelley transaction submit \
    --tx-file tx.signed \
    --testnet-magic 42

Y ahora tu llave de participación está registrada en la blockchain.
