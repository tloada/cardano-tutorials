# Registrar un _Stake Pool_ con Metadatos

**USÁ TAG: 1.14.2**

### Prerrequisitos

Asegurate que tengás acceso a:

* Una o más direcciones con fondos.
* Las llaves y el certificado funcional del _stake pool_.
* Las llaves de participación.

Por los momentos contamos con:

    | File           | Content                           |
    | -------------- | --------------------------------  |
    | `payment.vkey` | payment verification key          |
    | `payment.skey` | payment signing key               |
    | `stake.vkey`   | staking verification key          |
    | `stake.skey`   | staking signing key               |
    | `stake.addr`   | registered stake address          |
    | `payment.addr` | funded address linked to `stake`  |
    | `cold.vkey`    | cold verification key             |
    | `cold.skey`    | cold signing key                  |
    | `cold.counter` | issue counter                     |
    | `node.cert`    | operational certificate           |
    | `kes.vkey`     | KES verification key              |
    | `kes.skey`     | KES signing key                   |
    | `vrf.vkey`     | VRF verification key              |
    | `vrf.skey`     | VRF signing key                   |


Registrar tu stake pool requiere:

* Crear un archivo JSON con tu metadatos y guardala en el nodo y en un _url_ que mantengás.
* Obtener el _hash_ de tu archivo JSON
* Crear el certificado de registro de tu _stake pool_
* Crear un certificado de delegación (_pledge_)
* Presentar los certificados a la blockchain
* •	Presentar un _Pull Request_ (PR) con los metadatos de tu pool (paso temporal hasta que la sincronización de la base de datos sea actualizada)

**PRECAUCIÓN:** Crear el **certificado de registro de tu _stake pool**_ y el **certificado de delegación** requiere de las **llaves frías**. Así que vas a querer crear estos certificados en tu máquina local tomando las medidas de seguridad apropiadas para evitar exponer tus llaves frías al internet.  

### 1. Crear un archivo JSON con los metadatos de tu pool

    {
    "name": "TestPool",
    "description": "The pool that tests all the pools",
    "ticker": "TEST",
    "homepage": "https://teststakepool.com"
    }

Guardá el archivo en un _url_ que controles, por ejemplo [https://gist.githubusercontent.com/testPool/.../testPool.json](shorturl.at/gDV47)

### 2. Obtener el _hash_ de tu archivo:

Esto valida que el archivo JSON cumple con el esquema requerido, de ser el caso, vas a lograr obtener el _hash_ de tu archivo. 

    cardano-cli shelley stake-pool metadata-hash --pool-metadata-file testPool.json

    >6bf124f217d0e5a0a8adb1dbd8540e1334280d49ab861127868339f43b3948af
    
### 3. Crear el certificado de registro de tu _stake pool_

Crear un _stake pool registration certificate_ (certificado de registro de tu _stake pool_):

    cardano-cli shelley stake-pool registration-certificate \
    --cold-verification-key-file cold.vkey \
    --vrf-verification-key-file vrf.vkey \
    --pool-pledge 1000000000 \
    --pool-cost 100000000 \
    --pool-margin 0.05 \
    --pool-reward-account-verification-key-file stake.vkey \
    --pool-owner-stake-verification-key-file stake.vkey \
    --testnet-magic 42 \
    --pool-relay-port 3001 \
    --pool-relay-ipv4 123.123.123.123 \
    --metadata-url https://gist.githubusercontent.com/testPool/.../testPool.json \
    --metadata-hash 6bf124f217d0e5a0a8adb1dbd8540e1334280d49ab861127868339f43b3948af \
    --out-file pool.cert

| Parámetro                            | Explicación                                       |
|--------------------------------------|---------------------------------------------------|
| stake-pool-verification-key-file     | verificación de llave _fría_                      |
| vrf-verification-key-file            | verificación de llave _KES_                       |
| pool-pledge                          | pledge (compromiso) [en lovelace]                                  |
| pool-cost                            | costo operacional por época (en lovelace)            |
| pool-margin                          | margen del operador                                   |
| pool-reward-account-verification-key-file | verificación de la llave de participación para las recompensas          |
| pool-owner-staking-verification-key-file  | verificación de la(s) llave(s) de participación para los dueño(s) del pool |
| out-file                             | Archivo output para escribir el certificado a        |
| pool-relay-port                      | puerto                                              |
| pool-relay-ipv4                      | dirección ip del nodo de relevo                             |
| metadata-url                         | _url_ de tu archivo json                             |
| metadata-hash                        | el _hash_ del archivo json de tu pool              |


En el ejemplo anterior, usamos las llaves _**cold-**_ y _**VRF-**_ que creamos [aquí](llaves_pool.md),
comprometimos 100,00 ADA a nuestra pool, declaramos costos operacional de 10,000 ADA por época, establecimos el margen operacional (i.e la proporción de recompensas que obtenemos luego de los costos y antes de que el resto se distribuido entre los dueños y delegantes de acuerdo a su respectiva participación) a 5%, usamos la llave de participación que creamos [aquí](llaves_y_direcciones.md) para recibir nuestras recompensas y usamos la misma llave para el _pledge_ (compromiso) que el dueño del pool.

Podríamos usar una llave diferente para las recompensas, y podríamos proporcionar más de una llave del dueño si hubiera múltiples dueños que comparten el _pledge_.

El archivo _**pool.cert**_ debería de verse así:

    type: StakePoolCertificateShelley
    title: Free form text
    cbor-hex:
    18b58a03582062d632e7ee8a83769bc108e3e42a674d8cb242d7375fc2d97db9b4dd6eded6fd5820
    48aa7b2c8deb8f6d2318e3bf3df885e22d5d63788153e7f4040c33ecae15d3e61b0000005d21dba0
    001b000000012a05f200d81e820001820058203a4e813b6340dc790f772b3d433ce1c371d5c5f5de
    46f1a68bdf8113f50e779d8158203a4e813b6340dc790f772b3d433ce1c371d5c5f5de46f1a68bdf
    8113f50e779d80f6   

### 4. Crear el certificado de delegación (_pledge_)

Debemos de honrar nuestro _pledge_ delegando como mínimo la cantidad de _pledge_ a nuestro pool, así que debemos crear un _certificado de delegación_ para conseguir esto:

    cardano-cli shelley stake-address delegation-certificate \
    --stake-verification-key-file stake.vkey \
    --cold-verification-key-file cold.vkey \
    --out-file delegation.cert

Esto crea un certificado de delegación el cual delega fondos de todas las direcciones asociadas a la llave `stake.vkey` al pool perteneciente a la llave fría `cold.vkey`. Si en el primer paso hubiéramos usado diferentes llaves de participación para los dueños del pool, necesitaríamos crear certificados de delegación para todas las llaves en vez.

### 5. Presentar el certificado del pool y el certificado de delegación a la blockchain

Finalmente necesitamos presentar el certificado de registro de la pool y el(los) certificado(s) de delegación a la blockchain incluyéndolos en una o más transacciones. Podemos usar una transacción para múltiples certificados, los certificados serán aplicados en orden.

Como lo hicimos anteriormente, comenzamos calculando los costos (explicado [aquí](transacción.md)):

    cardano-cli shelley transaction calculate-min-fee \
    --tx-in-count 1 \
    --tx-out-count 1 \
    --ttl 200000 \
    --testnet-magic 42 \
    --signing-key-file payment.skey \
    --signing-key-file stake.skey \
    --signing-key-file cold.skey \
    --certificate-file pool.cert \
    --certificate-file delegation.cert \
    --protocol-params-file protocol.json

    > 184685

Notá cómo incluimos los dos certificados en el comando `calculate-min-fee` y que la transacción debería ser firmada por la llave de pago correspondiente a la diracción que usamos para pagar la transacción, la(s) llave(s) de participación del dueño(s) y la llave frío del nodo.

También debemos pagar el depósito para el registro del stake pool. El monto del depósito se especifica en el archivo génesis:

    "poolDeposit": 500000000

Para calcular las cantidades correctas, necesitamos consultar nuestra dirección, explicado [aquí](transacción.md).

Podemos obtener algo parecido a esto

                                TxHash                                 TxIx        Lovelace
    ----------------------------------------------------------------------------------------
    9db6cf...                                                            0      999999267766

Notá que los fondos disponibles son mayores que el _pledge_, lo cual está bien. No deben de ser _menores_.

En este ejemplo, ahora podemos calcular nuestro cambio:

    expr 999999267766 - 500000000 - 184685
    > 999499083081

Ahora podemos construir nuestra transacción:

    cardano-cli shelley transaction build-raw \
    --tx-in 9db6cf...#0 \
    --tx-out $(cat payment.addr)+999499083081 \
    --ttl 200000 \
    --fee 184685 \
    --out-file tx.raw \
    --certificate-file pool.cert \
    --certificate-file delegation.cert

Firmarla:

    cardano-cli shelley transaction sign \
    --tx-body-file tx.raw \
    --signing-key-file payment.skey \
    --signing-key-file stake.skey \
    --signing-key-file cold.skey \
    --testnet-magic 42 \
    --out-file tx.signed

Y presentarla:

    cardano-cli shelley transaction submit \
    --tx-file tx.signed \
    --testnet-magic 42

Para verificar que el registro de tu stake pool fue un éxito, podés hacer lo siguiente:

    cardano-cli shelley stake-pool id --verification-key-file <path to your cold.vkey>

dará como output tu _poolID_. Luego podés revisar la presencia de tu _poolID_ en el estado del _ledger_ (libro) de la red, con el siguiente comando:

    cardano-cli shelley query ledger-state --testnet-magic 42 | grep publicKey | grep <poolId>

o usando 

    cardano-cli shelley query ledger-state --testnet-magic 42 \
    | jq '._delegationState._pstate._pParams.<poolid>' 

Lo cual debería de regresar una cadena no-vacía si tu poolID está en el libro. Luego podés dirigirte a un listado de los pools como https://ff.pooltool.io/ y (confirmando que esté funcionando y mostrando un listado de las stake pools registradas) deberías de ser lograr encontrar tu pool en la página buscándola usando tu _poolID_, y posteriormente reclamarla (pueda que requiera registrarse a la página) y darle un nombre personalizado.


