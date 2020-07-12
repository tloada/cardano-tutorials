# Configurar los archivos de topología para los nodos productor de bloques y de relevo.

Antes de registrar nuestro _stake pool_, configuremos nuestros nodos **productor de bloques** y **de relevo**:

**OJO:** Aquí podés encontrar _peers_ (colegas) para conectarte, y presentar la información de tu propio nodo de relevo:  https://github.com/input-output-hk/cardano-ops/blob/master/topologies/ff-peers.nix#L5-L10

### Configurar el nodo productor de bloques

Conseguí los archivos de configuración para tu nodo productor de bloques si todavía no los tenés, por ejemplo

    mkdir config-files
    cd config-files     

    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/shelley_testnet-config.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/shelley_testnet-genesis.json
    wget https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/shelley_testnet-topology.json

Hacé que tu **nodo productor de bloques** se comunique solamente con **TU** nodo de relevo. No te olvidés de configurar tu firewall también:

    nano shelley_testnet-topology.json

  	{
  	  "Producers": [
  	    {
  	      "addr": "<RELAY IP ADDRESS",
  	      "port": <PORT>,
  	      "valency": 1
  	    }
  	  ]
  	}

### Configurar el nodo de relevo:

Hacé que tu **nodo de relevo** se comunique con tu **nodo productor de bloques** y con **otros relevos** en la red editando el archivo `shelley_testnet-topology.json`:


    nano shelley_testnet-topology.json

    {
      "Producers": [
        {
          "addr": "<BLOCK-PRODUCING IP ADDRESS",
          "port": PORT,
          "valency": 1
        },
        {
          "addr": "<IP ADDRESS>",
          "port": <PORT>,
          "valency": 1
        },
        {
          "addr": "<IP ADDRESS",
          "port": <PORT>,
          "valency": 1
        }
      ]
    }
