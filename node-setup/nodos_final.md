### Inicia tus nodos:

Primero reiniciamos nuestro **nodo de relevo** usando:

    cardano-node run \
    --topology shelley_testnet-topology.json \
    --database-path /db \
    --socket-path /db/node.socket \
    --host-addr <PUBLIC IP> \
    --port <PORT> \
    --config shelley_testnet-config.json

Luego, reiniciamos nuestro **nodo productor de bloques** usando:

    cardano-node run \
    --topology shelley_testnet-topology.json \
    --database-path /db \
    --socket-path /db/node.socket \
    --host-addr <PUBLIC IP> \
    --port <PORT> \
    --config shelley_testnet-config.json \
    --shelley-kes-key kes.skey \
    --shelley-vrf-key vrf.skey \
    --shelley-operational-certificate node.cert
