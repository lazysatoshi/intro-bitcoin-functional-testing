# Probando los tests Bitcoin Core
Estamos en disposición de probar nuestro primer test! Vayamos al directorio donde están ubicados los test funcionales de Bitcoin Core:
```
cd test/functional
```

Si miramos el contenido del directorio encontraremos unos 240 tests! Pero por ahora solo queremos probar que funcionan, para ello ejecutemos un test simple:

```
./p2p_block_sync.py
```

Si todo va bien veremos una salida como la que sigue:
```
2023-11-25T07:45:49.673000Z TestFramework (INFO): PRNG seed is: 7760672593178467557
2023-11-25T07:45:49.674000Z TestFramework (INFO): Initializing test directory /tmp/bitcoin_func_test_rp07nuzx
2023-11-25T07:45:50.081000Z TestFramework (INFO): Setup network: node0->node1->node2
2023-11-25T07:45:50.082000Z TestFramework (INFO): Mining one block on node0 and verify all nodes sync
2023-11-25T07:45:51.118000Z TestFramework (INFO): Success!
2023-11-25T07:45:51.170000Z TestFramework (INFO): Stopping nodes
2023-11-25T07:45:51.330000Z TestFramework (INFO): Cleaning up /tmp/bitcoin_func_test_rp07nuzx on exit
2023-11-25T07:45:51.330000Z TestFramework (INFO): Tests successful
```

Ya tenemos los test funcionando. El siguiente paso no es necesario hacerlo, solo es para mostrarte como sería ejecutar todos los test funcionales de Bitcoin Core

```
$ ./test_runner.py -j 10
Temporary test directory at /tmp/test_runner_₿_🏃_20231125_024838
Running Unit Tests for Test Framework Modules
.................
----------------------------------------------------------------------
Ran 17 tests in 13.492s

OK
1/284 - mempool_persist.py --descriptors passed, Duration: 19 s
2/284 - rpc_psbt.py --legacy-wallet passed, Duration: 23 s
3/284 - rpc_psbt.py --descriptors passed, Duration: 24 s
4/284 - mempool_updatefromblock.py passed, Duration: 49 s
5/284 - feature_maxuploadtarget.py passed, Duration: 61 s
6/284 - mining_getblocktemplate_longpoll.py passed, Duration: 68 s
7/284 - wallet_fundrawtransaction.py --descriptors passed, Duration: 37 s
8/284 - wallet_fundrawtransaction.py --legacy-wallet passed, Duration: 63 s
9/284 - feature_fee_estimation.py passed, Duration: 94 s
10/284 - wallet_bumpfee.py --legacy-wallet passed, Duration: 49 s
11/284 - wallet_bumpfee.py --descriptors passed, Duration: 45 s
12/284 - feature_segwit.py --descriptors passed, Duration: 14 s
13/284 - feature_segwit.py --descriptors --v2transport passed, Duration: 12 s
14/284 - p2p_segwit.py passed, Duration: 119 s
15/284 - feature_segwit.py --legacy-wallet passed, Duration: 25 s
16/284 - wallet_backup.py --descriptors passed, Duration: 37 s
17/284 - feature_abortnode.py passed, Duration: 6 s
18/284 - p2p_tx_download.py failed, Duration: 17 s
...
```

Estas son algunas de las cosas que hacen los desarrolladores de Bitcoin Core para asegurar que el software de tu nodo continuará siendo tan confiable como siempre.

# ¿Que Sigue?

Veámos con más detalle como funcionan los tests funcionales. Vaya a [Analizando los test](04_0_How-test-works.md)

