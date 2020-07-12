## Adquirir archivos genesis, config y topology; iniciar el nodo

Iniciar el nodo y conectarlo a la testnet requiere de 3 archivos de configuración:

* shelley_testnet-topology.json
* shelley_testnet-genesis.json
* shelley_testnet-config.json

Creemos un nuevo directorio dentro de `cardano-node` para guardar los archivos que necesitamos para iniciar el nodo.

    mkdir cardano-node/relay
    cd cardano-node/relay

Ahora podés descargar `shelley_testnet-config.json` `shelley_testnet-genesis.json` y `shelley_testnet-topology.json`. de: https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/index.html

O con la línea de comando usando:

    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/shelley_testnet-config.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/shelley_testnet-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/shelley_testnet-topology.json

Iniciá el nodo usa el comando `cardano-node run` y ciertos argumentos.

Podés conseguir la lista completa con `cardano-node run --help`  

	--topology FILEPATH             The path to a file describing the topology.
  	--database-path FILEPATH        Directory where the state is stored.
  	--socket-path FILEPATH          Path to a cardano-node socket
  	--host-addr HOST-NAME           Optionally limit node to one ipv6 or ipv4 address
  	--port PORT                     The port number
  	--config NODE-CONFIGURATION     Configuration file for the cardano-node
  	--validate-db                   Validate all on-disk database files
  	--shutdown-ipc FD               Shut down the process when this inherited FD reaches EOF
  	--shutdown-on-slot-synced SLOT  Shut down the process after ChainDB is synced up to the
  	                                specified slot
    -h,--help                       Show this help text

Iniciá tu nodo:


     cardano-node run \
       --topology path/to/shelley_testnet-topology.json \
       --database-path path/to/db \
       --socket-path path/to/db/node.socket \
       --host-addr x.x.x.x \
       --port 3001 \
       --config path/to/shelley_testnet-config.json

**OJOTE** Tenés que remplazar x.x.x.x con tu IP estático público e indicar el path correcto hacia tus archivos.

Podés verificar si tu nodo se está sincronizando, consiguiendo el _**tip**_ actual. (El `--testnet-magic 42` identifica la Shelley testnet)

        export CARDANO_NODE_SOCKET_PATH=path/to/db/node.socket
        cardano-cli shelley query tip --testnet-magic 42

        > Tip (SlotNo {unSlotNo = 74680}) (ShelleyHash {unShelleyHash = HashHeader {unHashHeader = edfefc4ac1e6a6ad1551bcf0650ade22f2e99937936bb61d8d7d5fae2e6a19aa}}) (BlockNo {unBlockNo = 2030})

La fase de sincronización puede tardar bastante tiempo.
