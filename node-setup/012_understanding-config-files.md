# Comprendiendo tus archivos de configuración y cómo usarlos:

ACTUALIZADO PARA _TAG_: 1.14.2

## El archivo topology.json

Le indica a tu nodo cuáles son los nodos en la red con los cuáles se debe comunicar. Una versión minimalista se ve así:


	{
	  "Producers": [
	    {
	      "addr": "x.x.x.x",
	      "port": 3001,
	      "valency": 1
	    }
	  ]
	}

* •	Esto significa que tu nodo contactará al nodo conectado al ip `x.x.x.x` en el `port (puerto) 3001`.

* `valency` le indica a tu nodo cuántas conexiones debería tener. Solo tiene efecto con direcciones dns. Si una dirección dns es dada, la _valency_ (valencia) indica cuántas direcciones ip resueltas debemos de mantener una conexión activa (_hot_); para direcciones ip, la valencia es usada como un valor booleano, donde `0` significa ignorar la dirección.

Tu nodo _**block-producing**_ (productor de bloques) debe de comunicarse _**SOLAMENTE**_ con tus nodos _**relay**_ (relevo), y el nodo de relevo debe de comunicarse con los demás nodos de relevo en la red. Unite a nuestro grupo de telegram para encontrar direcciones ip y puertos de tus _**peers**_ (colegas).


## El archivo genesis.json

El archivo genesis.json es creado con el `cardano-cli` cuando lee el archivo `genesis.spec.json` el cual está fuera de alcance para este documento. Pero es importante porque es usado para preparar:

* `genDelegs`, un mapeo desde las llaves génesis hasta los delegados génesis.
* `initialFunds`, un mapeo desde las direcciones iniciales hasta los valores iniciales de esas direcciones.
* `MaxLovelaceSupply`, la cantidad total de lovelaces en la blockchain.  
* `startTime`, el tiempo del _**slot**_ (espacio) cero.

El archivo `genesis.json` se verá como el siguiente.

		{
		"activeSlotsCoeff": 0.05,
		"protocolParams": {
		"poolDeposit": 500000000,
		"protocolVersion": {
			"minor": 0,
			"major": 0
		},
		"minUTxOValue": 0,
		"decentralisationParam": 1,
		"maxTxSize": 16384,
		"minPoolCost": 0,
		"minFeeA": 44,
		"maxBlockBodySize": 65536,
		"minFeeB": 155381,
		"eMax": 18,
		"extraEntropy": {
			"tag": "NeutralNonce"
		},
		"maxBlockHeaderSize": 1100,
		"keyDeposit": 400000,
		"nOpt": 250,
		"rho": 0.0022,
		"tau": 0.05,
		"a0": 0.3
		},
		"protocolMagicId": 42,
		"genDelegs": {
		"acded41a3329534b4a138a6262fc89ed84ab84a47439820027a1cfde": {
			"delegate": "3006bbc11b9587f758f818e55d376173894f3119fa9f3bb506b39014",
			"vrf": "4591768a404433121c891f4dcc72db18945f8f05c51b990c2ef32135f585ae5b"
		},
		"0e549da1e802e993fd67d0da974f8d64148417ded9ace34a38ee842e": {
			"delegate": "17d2ab1bd33faeb975ce82f531cffb4c956acf14fc5fe62a30209b55",
			"vrf": "f086cf388d1c9de15b5100da4198b5724ac65bab87ee25d1a7b34cbebd0dad36"
		},
		"a8fc4c8befb7a818fb86e68ec2bab42e90efb7833d6854e2d29570e7": {
			"delegate": "65a6cf060d4b537ecca6b09690740cf7633941dd1de086c06652461f",
			"vrf": "f92d64e5c607c3ff94ca8614781c5fc015d12c72c642b919e4d5665e765295b1"
		}
		},
		"updateQuorum": 3,
		"networkId": "Testnet",
		"initialFunds": {
		"60c307230dbe3be32364f2c3386c61b9a065b36635705f6feac72c4802": 1e+16,
		"60be9af17be11a9c959721278f7a4be0161688df2ff1965444f8f50d53": 1000000000000000
		},
		"maxLovelaceSupply": 45000000000000000,
		"networkMagic": 42,
		"epochLength": 21600,
		"staking": {
		"pools": {},
		"stake": {}
		},
		"systemStart": "2020-06-28T21:00:00Z",
		"slotsPerKESPeriod": 3600,
		"slotLength": 1,
		"maxKESEvolutions": 120,
		"securityParam": 108
		}

Aquí una pequeña descripción de cada parámetro. Podés aprender más en (el contenido está en inglés) [spec](https://github.com/input-output-hk/cardano-ledger-specs/tree/master/shelley/chain-and-ledger/executable-spec)


| PARÁMETRO | SIGNIFICADO |
|----------| --------- |
| activeSlotsCoeff | La proporción de _**slots**_ en la cual los bloques deben de ser emitidos. |
| poolDecayRate | La tasa de descomposición para depósitos en la _**pool**_ |
| poolDeposit | El monto del depósito de registro para una _**pool**_ |
| protocolVersion| Versiones de protocolo aceptadas |
| decentralisationParam | Porcentaje de bloques producidos por nodos federados |
| maxTxSize | Máximo tamaño de la transacción |
| minPoolCost | Las _**stake pools**_ no pueden registrar/re-registrar su participación con un costo menor a este valor |
| minFeeA | El factor lineal para el cálculo del costo mínimo |
| maxBlockBodySize | Tamaño máximo del cuerpo del bloque |
| keyMinRefund | El mínimo porcentaje de garantía del reembolso |
| minFeeB | El factor constante para calcular el mínimo impuesto |
| eMax | _**Epoch**_ (época) ligada al retiro de la _pool_ |
| extraEntropy | Bueno, entropía extra =) |
| maxBlockHeaderSize | |
| keyDeposit | El monto del depósito para registrar la llave |
| keyDecayRate | La tasa de descomposición del depósito |
| nOpt | Número deseado de _pools_ |
| rho | Expansión monetaria |
| poolMinRefund | Mínimo porcentaje del reembolso de la _**pool**_ |
| tau | Expansión de la tesorería |
| a0 | Influencia del _**pledge**_ (compromiso) de la _**pool**_ |
| protocolMagicId | Para identificar las _**testnets**_ |
| systemStart | Tiempo del _**slot**_ 0 |
| genDelegs | Mapeo de las llaves génesis para delegación génesis |                
| updateQuorum | Determina los requisitos necesarios para votos en la actualización del parámetro del protocolo |
| maxMajorPV | Provee un mecanismo para detener nodos desactualizados |
| initialFunds | Mapea los valores con su dirección |
| maxLovelaceSupply | La cantidad total de Lovelaces en el sistema, usada en la calculación de las recompensas |
| networkMagic | Para identificar la _**testnet**_ |
| epochLength | Número de _**slots**_ en una _**epoch**_ |
| staking | Delegación inicial |
| slotsPerKESPeriod | Número de _**slots**_ en un periodo _**KES**_ |
| slotLength | Duración del _**slot**_ en segundos |
| maxKESEvolutions | La máxima cantidad de veces que una llave _**KES**_ puede ser evolucionada antes que el operador de la _**pool**_ deba crear un nuevo certificado funcional |
| securityParam | Parámetro de seguridad k |


## The config.json file

The default `config.json` file that we downloaded is shown below.

This file has __4__ sections that allow you to have full control on what your node does and how the informtion is presented.

__NOTE Due to how the config.json file is generated, fields on the real file are shown in a different (less coherent) order. Here we present them in a more structured way__

### 1 Basic Node Configuration.

First section relates the basic node configuration parameters. Make sure you have to `TPraos`as the protocol, the correct path to the `shelley_testnet-genesis.json` file, `RequiresMagic`for its use in a testnet.
Note that in this example we are using the SimpleView. This will send the output to `stdout`. Other option is `LiveView` which uses a terminal multiplexer to generate a fancy view. We will cover this topic later.

	{
	  "Protocol": "TPraos",
	  "GenesisFile": "shelley_testnet-genesis.json",
	  "RequiresNetworkMagic": "RequiresMagic",

### 2 Update parameteres

This protocol version number gets used by block producing nodes as part of the system for agreeing on and synchronising protocol updates.You just need to be aware of the latest version supported by the network. You dont need to change anything here.

	  "ApplicationName": "cardano-sl",
	  "ApplicationVersion": 0,
	  "LastKnownBlockVersion-Alt": 0,
	  "LastKnownBlockVersion-Major": 0,
	  "LastKnownBlockVersion-Minor": 0,


### 3 Tracing

`Tracers` tell your node what information you are interested in when logging. Like switches that you can turn ON or OFF according the type and quantity of information that you are interesetd in. This provides fairly coarse grained control, but it is relatively efficient at filtering out unwanted trace output.

The node can run in either the `SimpleView` or `LiveView`. The `SimpleView` just uses standard output, optionally with log output. The `LiveView` is a text console with a live view of various node metrics.

`TurnOnLogging`: Enbles or disables logging overall.

`TurnOnLogMetrics`: Enable the collection of various OS metrics such as memory and CPU use. These metrics can be directed to the logs or monitoring backends.

`setupBackends`, `defaultBackends`, `hasEKG`and `hasPrometheus`: The system supports a number of backends for logging and monitoring. This settings list the the backends available to use in the configuration. The logging backend is called `Katip`.
Also enable the EKG backend if you want to use the EKG or Prometheus monitoring interfaces.

`setupScribes` and `defaultScribes`: For the Katip logging backend we must set up outputs (called scribes) The available types of scribe are:

* FileSK: for files
* StdoutSK/StderrSK: for stdout/stderr
* JournalSK: for systemd's journal system
* DevNullSK
* The scribe output format can be ScText or ScJson.

`rotation` The default file rotation settings for katip scribes, unless overridden in the setupScribes above for specific scribes.


	  "TurnOnLogging": true,
	  "TurnOnLogMetrics": true,
	  "ViewMode": "SimpleView",
	  "TracingVerbosity": "NormalVerbosity",
	  "minSeverity": "Debug",
	  "TraceBlockFetchClient": false,
	  "TraceBlockFetchDecisions": false,
	  "TraceBlockFetchProtocol": false,
	  "TraceBlockFetchProtocolSerialised": false,
	  "TraceBlockFetchServer": false,
	  "TraceChainDb": true,
	  "TraceChainSyncBlockServer": false,
	  "TraceChainSyncClient": false,
	  "TraceChainSyncHeaderServer": false,
	  "TraceChainSyncProtocol": false,
	  "TraceDNSResolver": true,
	  "TraceDNSSubscription": true,
	  "TraceErrorPolicy": true,
	  "TraceForge": true,
	  "TraceHandshake": false,
	  "TraceIpSubscription": true,
	  "TraceLocalChainSyncProtocol": false,
	  "TraceLocalErrorPolicy": true,
	  "TraceLocalHandshake": false,
	  "TraceLocalTxSubmissionProtocol": false,
	  "TraceLocalTxSubmissionServer": false,
	  "TraceMempool": true,
	  "TraceMux": false,
	  "TraceTxInbound": false,
	  "TraceTxOutbound": false,
	  "TraceTxSubmissionProtocol": false,
	  "setupBackends": [
	    "KatipBK"
	  ],
	  "defaultBackends": [
	    "KatipBK"
	  ],
	  "hasEKG": 12788,
	  "hasPrometheus": [
	    "127.0.0.1",
	    12798
	  ],
	  "setupScribes": [
	    {
	      "scFormat": "ScText",
	      "scKind": "StdoutSK",
	      "scName": "stdout",
	      "scRotation": null
	    }
	  ],
	  "defaultScribes": [
	    [
	      "StdoutSK",
	      "stdout"
	    ]
	  ],
	  "rotation": {
	    "rpKeepFilesNum": 10,
	    "rpLogLimitBytes": 5000000,
	    "rpMaxAgeHours": 24
	    },	  

### 4 Fine grained logging control

It is also possible to have more fine grained control over filtering of trace output, and to match and route trace output to particular backends. This is less efficient than the coarse trace filters above but provides much more precise control. `options`:

`mapBackends`This routes metrics matching specific names to particular backends. This overrides the defaultBackends listed above. And note that it is an **override** and not an extension so anything matched here will not go to the default backend, only to the explicitly listed backends.

`mapSubtrace` This section is more expressive, we are working on its documentation.


	  "options": {
	    "mapBackends": {
	      "cardano.node-metrics": [
	        "EKGViewBK",
	        {
	          "kind": "UserDefinedBK",
	          "name": "LiveViewBackend"
	        }
	      ],
	      "cardano.node.BlockFetchDecision.peers": [
	        "EKGViewBK",
	        {
	          "kind": "UserDefinedBK",
	          "name": "LiveViewBackend"
	        }
	      ],
	      "cardano.node.ChainDB.metrics": [
	        "EKGViewBK",
	        {
	          "kind": "UserDefinedBK",
	          "name": "LiveViewBackend"
	        }
	      ],
	      "cardano.node.metrics": [
	        "EKGViewBK",
	        {
	          "kind": "UserDefinedBK",
	          "name": "LiveViewBackend"
	        }
	      ]
	    },
	    "mapSubtrace": {
	      "benchmark": {
	        "contents": [
	          "GhcRtsStats",
	          "MonotonicClock"
	        ],
	        "subtrace": "ObservableTrace"
	      },
	      "#ekgview": {
	        "contents": [
	          [
	            {
	              "contents": "cardano.epoch-validation.benchmark",
	              "tag": "Contains"
	            },
	            [
	              {
	                "contents": ".monoclock.basic.",
	                "tag": "Contains"
	              }
	            ]
	          ],
	          [
	            {
	              "contents": "cardano.epoch-validation.benchmark",
	              "tag": "Contains"
	            },
	            [
	              {
	                "contents": "diff.RTS.cpuNs.timed.",
	                "tag": "Contains"
	              }
	            ]
	          ],
	          [
	            {
	              "contents": "#ekgview.#aggregation.cardano.epoch-validation.benchmark",
	              "tag": "StartsWith"
	            },
	            [
	              {
	                "contents": "diff.RTS.gcNum.timed.",
	                "tag": "Contains"
	              }
	            ]
	          ]
	        ],
	        "subtrace": "FilterTrace"
	      },

	      "cardano.epoch-validation.utxo-stats": {
	        "subtrace": "NoTrace"
	      },
	      "cardano.node-metrics": {
	        "subtrace": "Neutral"
	      },
	      "cardano.node.metrics": {
	        "subtrace": "Neutral"
	      }
	    }
	  }
	}
