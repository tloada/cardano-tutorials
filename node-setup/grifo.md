# Usando el grifo (faucet)

ACTUALIZADO PARA _TAG_: 1.14.2

El grifo te permite acceder a algo de “ada de prueba” (_test ada_), para que podás probar las funciones que requieren fondos.

Para poder usar el grifo, primero necesitás un dirección de pago (_payment address_). La cual puede ser asociada opcionalmente con una dirección de participación (_stake address_). Si no tenés tal dirección, seguí esta [guía](llaves_y_direcciones.md).

Para usar el grifo, simplemente ingresá

    curl -v -XPOST "https://faucet.shelley-testnet.dev.cardano.org/send-money/YOUR-ADDRESS

en la ventana de la terminal.
Si tu dirección está guardada en un archivo, por ejemplo 

    curl -v -XPOST "https://faucet.shelley-testnet.dev.cardano.org/send-money/$(cat payment.addr)"

Si todo sale bien, la última línea del output debe declarar success (éxitosa) e indicar cuántos fondos fueron enviados a tu dirección.

    *   Trying 3.122.86.4...
    * TCP_NODELAY set
    * Connected to faucet.shelley-testnet.dev.cardano.org (3.122.86.4) port 443 (#0)
    ...
    * Connection #0 to host faucet.shelley-testnet.dev.cardano.org left intact
    {"success":true,"amount":1000000000,"fee":168141,"txid":"8ed4383f7af20e81c9cef88b8aab0ff2b1b284dff0ed6614480f8dbfec7d6fb5"}

Ahora, revisá el balance de tu dirección para verificar que tengás los fondos:

    export CARDANO_NODE_SOCKET_PATH=~/cardano-node/relay/db/node.socket


Asegurate que tu nodo esté siendo ejecutado. Luego usá

    cardano-cli shelley query utxo \
    --address $(cat payment.addr) \
    --testnet-magic 42

Y deberías de ver algo como esto:

                              TxHash                                 TxIx        Lovelace
    ----------------------------------------------------------------------------------------
    65e99578e91dbf400c42989b5b5ae6dde877510900074f4afd8ff472639da6b3     0     1000000000000

    (Please note: although this command usually completes very quickly, under some conditions it could take severl minutes or even hours to complete.)


IMPORTANTE: Fondos sobrantes pueden ser regresados a la siguiente dirección: 00677291d73b71471afa49fe2d20b96f7227b05f863dafe802598964533e0dc3bc0cf7eb8153441db271a2288560378b209014350792f273bdc307f06ca34f0c6f
