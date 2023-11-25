# Analizando los test funcionales de Bitcoin Core

A continuación, veremos cómo funciona el framework de pruebas funcionales en Python de Bitcoin Core. Si necesitas una visión más detallada y rigurosa, te invito a que visites los siguientes enlaces:
* [Slides Chaincode Labs](https://telaviv2019.bitcoinedge.org/files/test-framework-in-bitcoin-core.pdf)
* [Functional Test Framework](https://github.com/chaincodelabs/bitcoin-core-onboarding/blob/main/functional_test_framework.asciidoc) 

El último enlace pertenece a "Onboarding Bitcoin Core", que es una fuente increíble de conocimientos sobre Bitcoin. Aquí tienes un par de enlaces:
* [Onboarding to Bitcoin Core Github Markdown documents](https://github.com/chaincodelabs/onboarding-to-bitcoin-core)
* [OBC Bitcoincore Academy](https://bitcoincore.academy/)

## Estructura de los test funcionales
En el directorio **test/functional** tenemos todos los test funcionales de Bitcoin Core asi como los constituyentes elementales del framework **test/functional/test_framework/**. Vemos que los nombres de los test siguen una nomenclatura precisa. Como prefijo del nombre del test tienen la categoria a la que pertenecen los tests (mempool, mining, rpc etc ..)
Podemos lanzar más de un test a la vez de la siguente forma:
```
test/functional/test_runner.py test/functional/wallet*
```
Volvamos al ejemplo usado en la introducción
```
from test_framework.test_framework import BitcoinTestFramework

class BlockSyncTest(BitcoinTestFramework):

    def set_test_params(self):
        self.setup_clean_chain = True
        self.num_nodes = 3

    def setup_network(self):
        self.setup_nodes()
        # Construct a network:
        # node0 -> node1 -> node2
        self.connect_nodes(0, 1)
        self.connect_nodes(1, 2)

    def run_test(self):
        self.log.info("Setup network: node0->node1->node2")
        self.log.info("Mining one block on node0 and verify all nodes sync")
        self.generate(self.nodes[0], 1)
        self.log.info("Success!")


if __name__ == '__main__':
    BlockSyncTest().main()
```
Lo analizaremos paso a paso. Primero importamos la libreria que nos permitirá usar el framework:
```
from test_framework.test_framework import BitcoinTestFramework
```

Ahora creamos la clase de para nuestro test. Todo test tiene que tener su clase, en este caso *BlockSyncTest* que tendrá como clase padre *BitcoinTestFramework*:
```
class BlockSyncTest(BitcoinTestFramework):
```

Para definir nuestro test deberemos sobreescribir el método *set_test_params*. En este caso, tendremos la cadena de bloques vacia y un red de tres nodos:
```
    def set_test_params(self):
        self.setup_clean_chain = True
        self.num_nodes = 3
```

Para configurar la red de tres nodos definida anteriormente, sobreescribimos el método *setup_network* que es un método heredado de la clase *BitcoinTestFramework*.
Mediante el *método connect_nodes* definiremos la topologia de nuestra red de bitcoin.
```

    def setup_network(self):
        self.setup_nodes()
        # Construct a network:
        # node0 -> node1 -> node2
        self.connect_nodes(0, 1)
        self.connect_nodes(1, 2)
```

Sobreescribimos el método *run_test* para definir los pasos que compondrán nuestro test. En este caso simple, solo mostrará mensajes y generará un nuevo bloque:
```
    def run_test(self):
        self.log.info("Setup network: node0->node1->node2")
        self.log.info("Mining one block on node0 and verify all nodes sync")
        self.generate(self.nodes[0], 1)
        self.log.info("Success!")
```

Por último tenemos, un bloque de código que se encarga de verificar si el archivo actual se está ejecutando como un programa principal o si se está importando como un módulo en otro archivo. Si el archivo se está ejecutando como un programa principal, se ejecutará el bloque de código dentro de esta condición. En este caso, se llama al método main() de la clase *BlockSyncTest* que es la clase que hemos creado para implementar nuestro test.

```
if __name__ == '__main__':
    BlockSyncTest().main()
```

Una vez visto en detalle el código de un test simple. Veámos diferentes formas de ejecutar el test: 
```
./p2p_block_sync.py  --loglevel=debug
```

Activando el debug veremos más detalles de los pasos que se ejecutan en el test. Otra opción interesante es 
```
./p2p_block_sync.py  --loglevel=debug --nocleanup
```
Con esta opción no se eliminan los ficheros generados durante el test. Por lo que tendremos acceso a los diferentes ficheros que generados por cada uno de los nodos de la red usada en el test. Si nos fijamos en la salida de la ejecución de un test, podremos ver la ubicación donde se guardan estos ficheros:


```
$ ./p2p_block_sync.py  --loglevel=debug --nocleanup
2023-11-25T11:34:12.887000Z TestFramework (INFO): PRNG seed is: 717117274335820803                                                                                               2023-11-25T11:34:12.887000Z TestFramework (DEBUG): Setting up network thread                                                                                                       
2023-11-25T11:34:12.888000Z TestFramework (INFO): Initializing test directory /tmp/bitcoin_func_test_g5yxz66h                                                                      
2023-11-25T11:34:12.890000Z TestFramework.node0 (DEBUG): bitcoind started, waiting for RPC to come up                                                                              
2023-11-25T11:34:12.891000Z TestFramework.node1 (DEBUG): bitcoind started, waiting for RPC to come up                                                                              
2023-11-25T11:34:12.891000Z TestFramework.node2 (DEBUG): bitcoind started, waiting for RPC to come up                                                                              
2023-11-25T11:34:13.146000Z TestFramework.node0 (DEBUG): RPC successfully started
2023-11-25T11:34:13.148000Z TestFramework.node1 (DEBUG): RPC successfully started
2023-11-25T11:34:13.150000Z TestFramework.node2 (DEBUG): RPC successfully started
2023-11-25T11:34:13.282000Z TestFramework (INFO): Setup network: node0->node1->node2
2023-11-25T11:34:13.282000Z TestFramework (INFO): Mining one block on node0 and verify all nodes sync
2023-11-25T11:34:13.282000Z TestFramework.node0 (DEBUG): TestNode.generate() dispatches `generate` call to `generatetoaddress`
2023-11-25T11:34:14.307000Z TestFramework (INFO): Success!
2023-11-25T11:34:14.307000Z TestFramework (DEBUG): Closing down network thread
2023-11-25T11:34:14.360000Z TestFramework (INFO): Stopping nodes
2023-11-25T11:34:14.360000Z TestFramework.node0 (DEBUG): Stopping node
2023-11-25T11:34:14.361000Z TestFramework.node1 (DEBUG): Stopping node
2023-11-25T11:34:14.362000Z TestFramework.node2 (DEBUG): Stopping node
2023-11-25T11:34:14.465000Z TestFramework.node0 (DEBUG): Node stopped
2023-11-25T11:34:14.466000Z TestFramework.node1 (DEBUG): Node stopped
2023-11-25T11:34:14.466000Z TestFramework.node2 (DEBUG): Node stopped
2023-11-25T11:34:14.466000Z TestFramework (WARNING): Not cleaning up dir /tmp/bitcoin_func_test_g5yxz66h
2023-11-25T11:34:14.466000Z TestFramework (INFO): Tests successful
```

De la salida anterior vemos que los ficheros se generarán en */tmp/bitcoin_func_test_g5yxz66h* y como hemos puesto la opción *--nocleanup*, no se eliminarán al finalizar la ejecución del test. El *datadir* será diferente en vuestro caso, ya que se genera con una cadena aleatoria. Revisar los logs de vuestra ejecución para determinar cuál es el vuestro.

Podemos observar que gracias a la opción debug, cada nodo va registrando su actividad en la salida del test:

```
2023-11-25T11:34:12.891000Z TestFramework.node2 (DEBUG): bitcoind started, waiting for RPC to come up                                                                              
```
En lina anterior el nodo 2 de la red creada arranca y está a la espera de inicializar el servicio RPC.

Otra opción muy interesante es la *--noshutdown*. De esta manera indicamos al test que no finalice los procesos *bitcoind* creados. Así podemos interactuar con ellos mediante *bitcoin-cli*. El siguiente ejemplo, nos conectamos via rpc a uno de los nodos creados durante el test. 
```
bitcoin-cli -regtest -rpcconnect=localhost -rpcport=16942 -datadir=/tmp/bitcoin_func_test_gc2vhe7z/node0/ getnetworkinfo
```

Analicemos los parámetros que le hemos proporcionado al *bitcoin-cli*. Le indicamos que usaremos *regtest*, con *-rpcconnect=localhost* le indicamos la ip en la cual está escuchando el *bitcoind*, -rpcport=16942 le indicamos el puerto rpc, se trata de un puerto no estandar. Hay que tener en cuenta que las instancias de *bitcoind* arrancadas por el test utilizan puertos no estandar tanto para servir peticiones rpc como para la intercomunicación entre los nodos que forman la red de bitcoin (normalmente se usa 8333 en mainnet). El último parámetro que le facilitamos es el *-datadir*, el cual es muy importante por varios motivos. Si no especificamos datadir irá al por defecto, que es el *$USER/.bitocoin/* y alli podemos tener otro bitcoin.conf que entre en conflicto. En nuestro *datadir* tenemos la cookie que permitirá a *bitcoin-cli* validarse contra *bitcoind*.

Una utilidad que puede ser interesante es la siguiente, que nos combina los logs te todas las instancias de *bitcoind* involucradas en el test, asi como los datos del test en una única salida

```
./combine_logs.py -c /tmp/bitcoin_func_test_lzzuvbhc | less -r
```

A continuación tenemos un extracto de la salida anterior. Si lo ejecutamos en nuestra computadora, la salida se verá coloreada según los tipos de logs mostrados:
```
 test  2023-11-25T11:34:12.887000Z TestFramework (INFO): PRNG seed is: 717117274335820803 
 test  2023-11-25T11:34:12.887000Z TestFramework (DEBUG): Setting up network thread 
 test  2023-11-25T11:34:12.888000Z TestFramework (INFO): Initializing test directory /tmp/bitcoin_func_test_g5yxz66h 
 test  2023-11-25T11:34:12.890000Z TestFramework.node0 (DEBUG): bitcoind started, waiting for RPC to come up 
 test  2023-11-25T11:34:12.891000Z TestFramework.node1 (DEBUG): bitcoind started, waiting for RPC to come up 
 test  2023-11-25T11:34:12.891000Z TestFramework.node2 (DEBUG): bitcoind started, waiting for RPC to come up 
 node0 2023-11-25T11:34:12.997567Z [init] [init/common.cpp:153] [LogPackageVersion] Bitcoin Core version v26.0rc2 (release build) 
 node0 2023-11-25T11:34:12.997597Z [init] [init.cpp:692] [InitParameterInteraction] InitParameterInteraction: parameter interaction: -bind set -> setting -listen=1 
 node1 2023-11-25T11:34:12.997699Z [init] [init/common.cpp:153] [LogPackageVersion] Bitcoin Core version v26.0rc2 (release build) 
 node1 2023-11-25T11:34:12.997726Z [init] [init.cpp:692] [InitParameterInteraction] InitParameterInteraction: parameter interaction: -bind set -> setting -listen=1 
 node0 2023-11-25T11:34:12.997744Z [init] [kernel/context.cpp:24] [Context] Using the 'sse4(1way),sse41(4way),avx2(8way)' SHA256 implementation 
 node0 2023-11-25T11:34:12.997753Z [init] [random.cpp:98] [ReportHardwareRand] Using RdSeed as an additional entropy source 
 node0 2023-11-25T11:34:12.997759Z [init] [random.cpp:101] [ReportHardwareRand] Using RdRand as an additional entropy source 
 node2 2023-11-25T11:34:12.997827Z [init] [init/common.cpp:153] [LogPackageVersion] Bitcoin Core version v26.0rc2 (release build) 
 node1 2023-11-25T11:34:12.997853Z [init] [kernel/context.cpp:24] [Context] Using the 'sse4(1way),sse41(4way),avx2(8way)' SHA256 implementation 
 node2 2023-11-25T11:34:12.997855Z [init] [init.cpp:692] [InitParameterInteraction] InitParameterInteraction: parameter interaction: -bind set -> setting -listen=1 
 node1 2023-11-25T11:34:12.997862Z [init] [random.cpp:98] [ReportHardwareRand] Using RdSeed as an additional entropy source 
 node1 2023-11-25T11:34:12.997868Z [init] [random.cpp:101] [ReportHardwareRand] Using RdRand as an additional entropy source 
 node2 2023-11-25T11:34:12.997980Z [init] [kernel/context.cpp:24] [Context] Using the 'sse4(1way),sse41(4way),avx2(8way)' SHA256 implementation 
 node2 2023-11-25T11:34:12.997989Z [init] [random.cpp:98] [ReportHardwareRand] Using RdSeed as an additional entropy source 
 node2 2023-11-25T11:34:12.997995Z [init] [random.cpp:101] [ReportHardwareRand] Using RdRand as an additional entropy source 
 node1 2023-11-25T11:34:13.000700Z [init] [init/common.cpp:124] [StartLogging] Default data directory /home/kali/.bitcoin 
 node1 2023-11-25T11:34:13.000712Z [init] [init/common.cpp:125] [StartLogging] Using data directory /tmp/bitcoin_func_test_g5yxz66h/node1/regtest 
 node0 2023-11-25T11:34:13.000715Z [init] [init/common.cpp:124] [StartLogging] Default data directory /home/kali/.bitcoin 
 node1 2023-11-25T11:34:13.000726Z [init] [init/common.cpp:130] [StartLogging] Config file: /tmp/bitcoin_func_test_g5yxz66h/node1/bitcoin.conf 
 node0 2023-11-25T11:34:13.000727Z [init] [init/common.cpp:125] [StartLogging] Using data directory /tmp/bitcoin_func_test_g5yxz66h/node0/regtest 
```

A modo de resumen veámos la estructura de directorios creada por nuestro test:
```
/tmp/bitcoin_func_test_g5yxz66h
├── node0
│   ├── bitcoin.conf
│   ├── regtest
│   │   ├── banlist.json
│   │   ├── blocks
│   │   │   ├── blk00000.dat
│   │   │   ├── index
│   │   │   │   ├── 000003.log
│   │   │   │   ├── CURRENT
│   │   │   │   ├── LOCK
│   │   │   │   └── MANIFEST-000002
│   │   │   └── rev00000.dat
│   │   ├── chainstate
│   │   │   ├── 000005.ldb
│   │   │   ├── 000006.log
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   └── MANIFEST-000004
│   │   ├── debug.log
│   │   ├── fee_estimates.dat
│   │   ├── mempool.dat
│   │   ├── peers.dat
│   │   ├── settings.json
│   │   └── wallets
│   ├── stderr
│   │   └── tmptqvinhac
│   └── stdout
│       └── tmpjfyqa89e
├── node1
│   ├── bitcoin.conf
│   ├── regtest
│   │   ├── banlist.json
│   │   ├── blocks
│   │   │   ├── blk00000.dat
│   │   │   ├── index
│   │   │   │   ├── 000003.log
│   │   │   │   ├── CURRENT
│   │   │   │   ├── LOCK
│   │   │   │   └── MANIFEST-000002
│   │   │   └── rev00000.dat
│   │   ├── chainstate
│   │   │   ├── 000005.ldb
│   │   │   ├── 000006.log
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   └── MANIFEST-000004
│   │   ├── debug.log
│   │   ├── fee_estimates.dat
│   │   ├── mempool.dat
│   │   ├── peers.dat
│   │   ├── settings.json
│   │   └── wallets
│   ├── stderr
│   │   └── tmp7ubx8yc3
│   └── stdout
│       └── tmpdt9hrt99
├── node2
│   ├── bitcoin.conf
│   ├── regtest
│   │   ├── banlist.json
│   │   ├── blocks
│   │   │   ├── blk00000.dat
│   │   │   ├── index
│   │   │   │   ├── 000003.log
│   │   │   │   ├── CURRENT
│   │   │   │   ├── LOCK
│   │   │   │   └── MANIFEST-000002
│   │   │   └── rev00000.dat
│   │   ├── chainstate
│   │   │   ├── 000005.ldb
│   │   │   ├── 000006.log
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   └── MANIFEST-000004
│   │   ├── debug.log
│   │   ├── fee_estimates.dat
│   │   ├── mempool.dat
│   │   ├── peers.dat
│   │   ├── settings.json
│   │   └── wallets
│   ├── stderr
│   │   └── tmp7gmdww4a
│   └── stdout
│       └── tmp6ma7zext
└── test_framework.log

```
Para cada uno de los nodos tenemos todos sus ficheros de configuración, los datos de su blockchain, sus logs etc ...
Solo nos queda parar los diferentes nodos creados, para ello ejecutaremos el comando *stop* para cada uno de ellos:

```
bitcoin-cli -datadir=/tmp/bitcoin_func_test_eitv9bv9/node0 stop
```
Tener en cuenta que el *datadir* será diferente en vuestro caso.
