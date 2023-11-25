# Analizando los test funcionales de Bitcoin Core

A continuación, veremos cómo funciona el framework de pruebas funcionales en Python de Bitcoin Core. Si necesitas una visión más detallada y rigurosa, te invito a que visites los siguientes enlaces:
* [Slides Chaincode Labs](https://telaviv2019.bitcoinedge.org/files/test-framework-in-bitcoin-core.pdf)
* [Functional Test Framework](https://github.com/chaincodelabs/bitcoin-core-onboarding/blob/main/functional_test_framework.asciidoc) 

El último enlace pertenece a "Onboarding Bitcoin Core", que es una fuente increíble de conocimientos sobre Bitcoin. Aquí tienes un par de enlaces:
* [Onboarding to Bitcoin Core Github Markdown documents](https://github.com/chaincodelabs/onboarding-to-bitcoin-core)
* [OBC Bitcoincore Academy](https://bitcoincore.academy/)

## Estructura de los test funcionales
En el directorio *test/functional* tenemos todos los test funcionales de Bitcoin Core asi como los constituyentes elementales del framework **test/functional/test_framework/**. Vemos que los nombres de los test siguen una nomenclatura precisa. Como prefijo del nombre del test tienen la categoria a la que pertenecen los tests (mempool, mining, rpc etc ..)
Podemos lanzar más de un test a la vez de la siguente forma:
```
test/functional/test_runner.py test/functional/wallet*
```



