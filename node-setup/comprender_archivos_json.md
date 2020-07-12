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
| poolDecayRate | La tasa de descomposición para depósitos en el _**pool**_ |
| poolDeposit | El monto del depósito de registro para un _**pool**_ |
| protocolVersion| Versiones de protocolo aceptadas |
| decentralisationParam | Porcentaje de bloques producidos por nodos federados |
| maxTxSize | Máximo tamaño de la transacción |
| minPoolCost | Los _**stake pools**_ no pueden registrar/re-registrar su participación con un costo menor a este valor |
| minFeeA | El factor lineal para el cálculo del costo mínimo |
| maxBlockBodySize | Tamaño máximo del cuerpo del bloque |
| keyMinRefund | El mínimo porcentaje de garantía del reembolso |
| minFeeB | El factor constante para calcular el mínimo impuesto |
| eMax | _**Epoch**_ (época) ligada al retiro del _pool_ |
| extraEntropy | Bueno, entropía extra =) |
| maxBlockHeaderSize | |
| keyDeposit | El monto del depósito para registrar la llave |
| keyDecayRate | La tasa de descomposición del depósito |
| nOpt | Número deseado de _pools_ |
| rho | Expansión monetaria |
| poolMinRefund | Mínimo porcentaje del reembolso del _**pool**_ |
| tau | Expansión de la tesorería |
| a0 | Influencia del _**pledge**_ (compromiso) del _**pool**_ |
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
| maxKESEvolutions | La máxima cantidad de veces que una llave _**KES**_ puede ser evolucionada antes que el operador del _**pool**_ deba crear un nuevo certificado funcional |
| securityParam | Parámetro de seguridad k |


## El archivo config.json

El archivo `config.json` que descargamos por default contiene lo que se muestra a continuación.

TEste archive tiene **4** secciones que te permiten tener control total sobre qué hace tu nodo y cómo es presentada la información.

**OJO: Debido a cómo el archivo config.json es generado, los campos en el archivo real son mostrados en un orden diferente (menos coherente). Aquí los presentamos en una forma más estructurada**

### 1 Configuración Básica del Nodo.

La primera sección trata sobre los parámetros básicos para la configuración del nodo. Asegurate que tengás `TPraos` como el protocolo, la ruta correcta hacia el archivo `shelley_testnet-genesis.json`, `RequiresMagic` para su uso en la _testnet_.
Notá que en este ejemplo estamos usando la SimpleView. Esto mandará el _output_ hacia `stdout`. Otra opción es `LiveView` la cual usa una terminal _multiplexer_ para generar una vista elegante. Vamos a cubrir ese tema más adelante.

	{
	  "Protocol": "TPraos",
	  "GenesisFile": "shelley_testnet-genesis.json",
	  "RequiresNetworkMagic": "RequiresMagic",

### 2 Parámetros de Actualización

Este número de versión del protocolo es usado por el nodo productor de bloques como parte del sistema para concordar y sincronizar actualizaciones del protocolo. Solo necesitás estar al tanto de la versión más reciente usada en la por la red. No necesitás cambiar nada aquí.

	  "ApplicationName": "cardano-sl",
	  "ApplicationVersion": 0,
	  "LastKnownBlockVersion-Alt": 0,
	  "LastKnownBlockVersion-Major": 0,
	  "LastKnownBlockVersion-Minor": 0,


### 3 Rastreo

`Tracers` (rastreadores) le indican a tu nodo qué información te interesa cuando inicias tu sesión. Como interruptores que podés “encender” y “apagar” de acuerdo con el tipo y cantidad de información que te interesa. Esto provee un control general, pero es relativamente eficiente en filtrar el output del rastreo no deseado.

El nodo puede ejecutarse en `SimpleView` o `LiveView`. `SimpleView` utiliza solamente output estándar, opcionalmente con el output del registro. `LiveView` es una consola de texto con una vista en vivo de varias métricas del nodo.

`TurnOnLogging`: Habilita o deshabilita el registro en general.

`TurnOnLogMetrics`: Habilita la recolección de varias métricas del sistema operativo como la memoria y el uso del CPU. Estas métricas pueden ser dirigidas a los registros o monitoreo de backend

`setupBackends`, `defaultBackends`, `hasEKG` y `hasPrometheus`: El sistema soporta un número de backends para registro y monitoreo. Estas configuraciones listan los backends disponibles para ser usados en la configuración. El registro backend se le conoce como `Katip`.
También habilita el backend _EKG_ si querés usar las interfaces de monitoreo _EKG_ o _Prometheus_.

`setupScribes` y `defaultScribes`: Para el registro backend de Katnip debemos de preparar outputs (llamados _scribes_). Los tipos de _scribes_ disponibles son:

* FileSK: para archivos
* StdoutSK/StderrSK: para stdout/stderr
* JournalSK: para el sistema _systemd journal_
* DevNullSK
* El formato del output del _scribe_ puede ser ScText o ScJson.

`rotation` La configuración de rotación por default del archivo para katnip _scribes_, a menos que sea anulada en la formación de _Scribes_ mencionada anteriormente para _scribes_ en específico.


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

### 4 Control de registro específico

De igual manera es posible tener un control más específico al momento de filtrar el rastreo del output, y al emparejar y enviar el rastreo del output hacia backends en particular. Esto es menos eficiente que los filtros de rastreo general mencionados anteriormente, pero provee un control mucho más preciso. `options`:

`mapBackends` Esto envía métricas que coinciden con nombres en específico hacia backends particulares. Esto anula los default backends que fueron listados anteriormente. Y tomá en cuenta que es una _**anulación**_ y no una extensión así que cualquier cosa que coincida aquí no será enviada al default backend, solamente a los backends explícitamente listados.

`mapSubtrace` Esta sección es más expresiva, estamos trabajando en su documentación.


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
