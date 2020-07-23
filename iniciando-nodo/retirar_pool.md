# Retirando un Stake Pool


Asumimos que temenos una stake pool registrada con llaves frías `cold.vkey` and `cold.skey`, una dirección de pago con fondos suficientes (para los costos de la transacción) `payment.addr` y una llave de pago para firmar asociada `payment.skey`.

Para retirar nuestro stake pool necesitamos:

* Crear un _certificado de cancelación del registro_ y
* Presentar el certificado a la blockchain con una transacción

El certificado de cancelación del registro contiene la _epoch_ (época) en la cual queremos retirar el pool.

Esta _epoch_ debe de ocurrir _después_ de la _epoch_ actual y a más tardar en `eMax` _epochs_ en el futuro, donde `eMax` es un parámetro del protocolo.

Primeramente, debemos de averiguar la _epoch_ actual. El número de _slots_ por _epoch_ se encuentra en el archivo génesis, y podemos obtenerlo con

    cat shelley_testnet-genesis.json | grep epoch
    > "epochLength": 21600,

Por lo tanto, una _epoch_ dura 21600 slots. Podemos obtener el _slot_ actual consultando el _tip_:

    export CARDANO_NODE_SOCKET_PATH=relay-db/node-socket
    cardano-cli shelley query tip --testnet-magic 42

    > Tip (SlotNo {unSlotNo = 856232}) ...

Esto nos da

    expr 856232 / 21600
    > 39

Por lo que nos encontramos en la _epoch_ 39.

Podemos averiguar el `eMax` consultando los parámetros actuales del protocolo:

    cardano-cli shelley query protocol-parameters \
    --testnet-magic 42 \
    --out-file protocol.json

    cat protocol.json | grep eMax
    > "eMax": 100,

Esto significa que lo más pronto que podemos retirarnos es en la _epoch_ 40 (uno en el futuro), y lo más tarde es 139 (_epoch_ actual más `eMax`).  

Por ejemplo, podemos decidir retirarnos en la _epoch_ 41.

### Crear certificado de cancelación del registro

**PRECAUCIÓN** Esto requiere de las **llaves frías**. Tomá las precauciones necesarias para no exponer tus llaves frías al internet.

Creá el certificado de cancelación del registro y guárdalo como `pool.dereg`:

    cardano-cli shelley stake-pool deregistration-certificate \
    --cold-verification-key-file cold.vkey \
    --epoch 41 \
    --out-file pool.dereg

### Construir, firmar y presentar la transacción:

Calculá los costos:

    cardano-cli shelley transaction calculate-min-fee \
    --tx-in-count 1 \
    --tx-out-count 1 \
    --ttl 860000 \
    --testnet-magic 42 \
    --signing-key-file payment.skey \
    --signing-key-file cold.skey \
    --certificate pool.dereg \
    --protocol-params-file protocol.json
    > 171309

Consultamos nuestra direcciones para un UTXO adecuado para usarlo como input:

    cardano-cli shelley query utxo \
    --address $(cat payment.addr) \
    --testnet-magic 42



           TxHash             TxIx        Lovelace
    ------------------------------------------------
    9db6cf...                    0      999999267766

Calculamos nuestro cambio:

    expr 999999267766 - 171309
    > 999999096457

Construimos la transacción _raw_:

    cardano-cli shelley transaction build-raw \
        --tx-in 9db6cf...#0 \
        --tx-out $(cat payment.addr)+999999096457 \
        --ttl 860000 \
        --fee 171309 \
        --out-file tx.raw \
        --certificate-file pool.dereg

Firmalas con ambas llaves, la llave de pago para firmar y la llave fría para firmar (la primera firma es necesaria porque estamos usando fondos de nuestra `paymant.addr`,
y la segunda porque el certificado necesitas ser firmado por el dueño del pool):

    cardano-cli shelley transaction sign \
        --tx-body-file tx.raw \
        --signing-key-file payment.skey \
        --signing-key-file cold.skey \
        --testnet-magic 42 \
        --out-file tx.signed

Y presentarlo a la blockchain:

    cardano-cli shelley transaction submit \
        --tx-file tx.signed \
        --testnet-magic 42

Y eso es todo. El pool será retirado al final de la _epcoh_ 40. Si cambiamos de opinión, podemos crear y presentar un nuevo certificado de registro antes de la _epoch_ 41, el cual anulará el certificado de cancelación del registro.
