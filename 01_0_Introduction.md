# Capitulo Uno: Introducion a los test funcionales de Bitcoin Core

## Introducion

Bitcoin Core entre otros tipos de test ( test unitarios, fuzz etc ..) dispone de los test funcionales. Existen un gran numero de test funcionales para Bitcoin core. Los test funcionales en Bitcoin Core son una parte esencial del proceso de desarrollo y mantenimiento de Bitcoin. Estos tests se utilizan para verificar que el software de Bitcoin Core funcione correctamente y cumpla con los requisitos esperados. Son esenciales para el proceso de desarrollo.

La intencion de este documento es dar una visión general de los test funcionales y ver algunas aplicaciones prácticas de como podemos usar el framework de los test para otros usos. Estos usos pretenden ser meramente didácticos y tienen la intención de ayudar a comprender que es bitcoin core y como funciona.

A modo de ejemplo veamos que infraestructura de nodos bitcoin podemos crear con un test sencillo. A conitnuacion tenemos un extracto del test bitcoin/test/functional/p2p_block_sync.py

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

Si lo ejecutamos de la siguiente forma:

```
./p2p_block_sync_lazy.py  --nocleanup --noshutdown
```

Nos crea la siguiente red regtest de bitcoin en nuestro ordenador:

```
$ ps -edaf | grep bitcoin                                                                                                                                                                                       
kali       47457       1  0 01:17 pts/1    00:00:07 /home/kali/bitshala/bitcoin/src/bitcoind -datadir=/tmp/bitcoin_func_test_gc2vhe7z/node0 -logtimemicros -debug -debugexclude=libevent -debugexclude=leveldb -debugexclude=rand -uacomment=testnode0 -disablewallet -logthreadnames -logsourcelocations -loglevel=trace
kali       47458       1  0 01:17 pts/1    00:00:08 /home/kali/bitshala/bitcoin/src/bitcoind -datadir=/tmp/bitcoin_func_test_gc2vhe7z/node1 -logtimemicros -debug -debugexclude=libevent -debugexclude=leveldb -debugexclude=rand -uacomment=testnode1 -disablewallet -logthreadnames -logsourcelocations -loglevel=trace
kali       47459       1  0 01:17 pts/1    00:00:07 /home/kali/bitshala/bitcoin/src/bitcoind -datadir=/tmp/bitcoin_func_test_gc2vhe7z/node2 -logtimemicros -debug -debugexclude=libevent -debugexclude=leveldb -debugexclude=rand -uacomment=testnode2 -disablewallet -logthreadnames -logsourcelocations -loglevel=trace
kali       65801    2844  0 01:54 pts/1    00:00:00 grep --color=auto bitcoin

```

Veamos los puertos por donde escuchan y se comunican la regtest formada por los 3 nodos:

```
ss -nlp | grep bitcoin                                                                                                                                                                                        
tcp   LISTEN 0      128                                             127.0.0.1:16942            0.0.0.0:*    users:(("bitcoind",pid=47457,fd=12))      
tcp   LISTEN 0      128                                             127.0.0.1:16943            0.0.0.0:*    users:(("bitcoind",pid=47458,fd=12))      
tcp   LISTEN 0      128                                             127.0.0.1:16944            0.0.0.0:*    users:(("bitcoind",pid=47459,fd=12))      
tcp   LISTEN 0      4096                                            127.0.0.1:11942            0.0.0.0:*    users:(("bitcoind",pid=47457,fd=19))      
tcp   LISTEN 0      4096                                            127.0.0.1:11943            0.0.0.0:*    users:(("bitcoind",pid=47458,fd=19))      
tcp   LISTEN 0      4096                                            127.0.0.1:11944            0.0.0.0:*    users:(("bitcoind",pid=47459,fd=19))      
tcp   LISTEN 0      4096                                            127.0.0.1:18445            0.0.0.0:*    users:(("bitcoind",pid=47458,fd=20))    
```

Podemos acceder al directorio de uno de los nodos y ver su contenido:

```
$ tree /tmp/bitcoin_func_test_gc2vhe7z/node0                                                                                                                                                                    
/tmp/bitcoin_func_test_gc2vhe7z/node0
├── bitcoin.conf
├── regtest
│   ├── banlist.json
│   ├── bitcoind.pid
│   ├── blocks
│   │   ├── blk00000.dat
│   │   ├── index
│   │   │   ├── 000003.log
│   │   │   ├── CURRENT
│   │   │   ├── LOCK
│   │   │   └── MANIFEST-000002
│   │   └── rev00000.dat
│   ├── chainstate
│   │   ├── 000005.ldb
│   │   ├── 000006.log
│   │   ├── CURRENT
│   │   ├── LOCK
│   │   └── MANIFEST-000004
│   ├── debug.log
│   ├── peers.dat
│   ├── settings.json
│   └── wallets
├── stderr
│   └── tmps04a9h53
└── stdout
    └── tmpplk8i4xd
```

Tambien podemos ver el fichero de configuración de uno de los nodos:

```
$ cat /tmp/bitcoin_func_test_gc2vhe7z/node0/bitcoin.conf                                                                                                                                                        
regtest=1
[regtest]
port=11942
rpcport=16942
rpcservertimeout=99000
rpcdoccheck=1
fallbackfee=0.0002
server=1
keypool=1
discover=0
dnsseed=0
fixedseeds=0
listenonion=0
peertimeout=999999999
printtoconsole=0
upnp=0
natpmp=0
shrinkdebugfile=0
deprecatedrpc=create_bdb
unsafesqlitesync=1
connect=0
bind=127.0.0.1
```

Para acabar con esta introducción veamos como nos podemos conectar a uno de estos nodos usando *bitcoin-cli*:

```
$ bitcoin-cli -regtest -rpcconnect=localhost -rpcport=16942 -datadir=/tmp/bitcoin_func_test_gc2vhe7z/node0/ getpeerinfo
[
  {
    "id": 0,
    "addr": "127.0.0.1:11943",
    "addrbind": "127.0.0.1:32782",
    "network": "not_publicly_routable",
    "services": "0000000000000409",
    "servicesnames": [
      "NETWORK",
      "WITNESS",
      "NETWORK_LIMITED"
    ],
    "relaytxes": true,
    "lastsend": 1700895797,
    "lastrecv": 1700895797,
    "last_transaction": 0,
    "last_block": 0,
    "bytessent": 2426,
    "bytesrecv": 2138,
    "conntime": 1700893036,
    "timeoffset": 0,
    "pingtime": 0.000311,
    "minping": 0.000217,
    "version": 70016,
    "subver": "/Satoshi:26.0.0(testnode1)/",
    "inbound": false,
    "bip152_hb_to": false,
    "bip152_hb_from": false,
    "startingheight": 0,
    "presynced_headers": -1,
    "synced_headers": -1,
    "synced_blocks": -1,
    "inflight": [
    ],
    "addr_relay_enabled": true,
    "addr_processed": 0,
    "addr_rate_limited": 0,
    "permissions": [
    ],
    "minfeefilter": 0.00001000,
    "bytessent_per_msg": {
      "block": 275,
      "feefilter": 64,
      "getaddr": 24,
      "getheaders": 93,
      "headers": 131,
      "inv": 61,
      "ping": 768,
      "pong": 768,
      "sendaddrv2": 24,
      "sendcmpct": 33,
      "verack": 24,
      "version": 137,
      "wtxidrelay": 24
    },
    "bytesrecv_per_msg": {
      "feefilter": 64,
      "getdata": 61,
      "getheaders": 186,
      "headers": 25,
      "ping": 768,
      "pong": 768,
      "sendaddrv2": 24,
      "sendcmpct": 33,
      "sendheaders": 24,
      "verack": 24,
      "version": 137,
      "wtxidrelay": 24
    },
    "connection_type": "manual",
    "transport_protocol_type": "v1",
    "session_id": ""
  }
]

```

# ¿Que Sigue?

Está listo para sumergirse en el curso, vaya a [Requerimientos e instalación de Bitcoin Core](02_0_Bitcoin-Core-Setup.md)
