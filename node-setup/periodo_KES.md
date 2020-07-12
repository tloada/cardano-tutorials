## Periodos KES

En el tutorial anterior observamos que, para crear un certificado funcional para un nodo productor de bloques, necesitás crear un _KES key pair_ (par de llaves KES).

"KES" significa _**K**ey **E**volving **S**ignature_ (Firma de Llave Evolutiva), lo cual quiere decir que cada cierto _periodo_, la llave _evoluciona_ a una nueva llave y descarta su versión anterior. Esto es útil porque significa que aun cuando un _hacker_ compromete la llave y obtiene acceso a la llave de firma, solamente puede usar esa llave para firmar bloques _a partir de ese momento_, pero no bloques de _periodos anteriores_, haciendo imposible para el _hacker_ rescribir la historia.

Por desgracia, hay un truco: una llave KES solamente puede evolucionar cierto número de periodos y volverse inservible posteriormente. Esto significa que antes de que ese número de periodos haya pasado, el operador del nodo debe de crear un nuevo par de llaves KES, emitir un nuevo certificado funcional del nodo con ese nuevo par y reiniciar el nodo con el nuevo certificado.

Para saber cuánto dura un periodo y por cuánto tiempo puede evolucionar una llave, podemos revisar el archivo _genesis.json_. Si el nombre de ese archivo es `shelley_testnet-genesis.json`, podemos teclear

    cat shelley_testnet-genesis.json | grep KES

    > "slotsPerKESPeriod": 3600,
    > "maxKESEvolutions": 120,

Y podemos ver que, en este ejemplo, la llave evolucionará después de cada periodo de 3600 _slots_ y que puede evolucionar 120 veces antes de que sea necesario renovarla.

Antes de crear un certificado funcional para nuestro nodo, necesitamos saber el inicio del periodo de validación KES, i.e. en qué periodo de evolución KES nos encontramos. Revisamos el _slot_ actual (asumiendo que el _socket_ (enchufe) de nuestro nodo de relevo se encuentra en `~/cardano-node/relay/db/node.socket`):

    export CARDANO_NODE_SOCKET_PATH=~/cardano-node/relay/db/node.socket
    cardano-cli shelley query tip --testnet-magic 42
    > Tip (SlotNo {unSlotNo = 432571}) ...

En este ejemplo, actualmente nos encontramos en el _slot_ 432571, y sabemos de nuestro archivo génesis que cada periodo dura 3600 _slots_. Así que calculamos el periodo actual

    expr 432571 / 3600
    > 120

De esta manera podemos crear un certificado funcional para nuestro _stake pool_ (asumiendo que los nombres de los archivos sean los mismos que usamos [aquí](llaves_pool.md)):

    cardano-cli shelley node issue-op-cert \
        --kes-verification-key-file kes.vkey \
        --cold-signing-key-file cold.skey \
        --operational-certificate-issue-counter cold.counter \
        --kes-period 120 \
        --out-file node.cert
