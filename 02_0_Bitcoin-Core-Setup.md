# Requerimientos para usar test funcionales de python de Bitcoin Core
Para poder experimentar con los test funcionales y ser capaces de reproducir los ejemplos mostrados en la introducción deberemos realizar los siguientes pasos:
* Descargar Bitcoin Core
* Compilar Bitcoin Core usando una parametrización especial para habilitar los test
* Validar que los test funcionan

# Descargar Bitcoin Core

Los pasos aqui descritos están extraido de la magnifica guia [Jon's Atack Guide](https://jonatack.github.io/articles/how-to-compile-bitcoin-core-and-run-the-tests) te animo a que la leas para tener una visión más detallada y rigurosa delo que se explica a continuación.

Clonamos el repositorio de Bitcoin Core
```
git clone https://github.com/bitcoin/bitcoin.git
```

Nos aseguramos de tener instalado en nuestro sistema linux las librerias de Berkeley Database:

```
$ dpkg -l | grep bdb
ii  libdb++-dev:amd64                              5.3.2                                 amd64        Berkeley Database Libraries for C++ [development]
ii  libdb-dev:amd64                                5.3.2                                 amd64        Berkeley Database Libraries [development]
ii  libdb5.3:amd64                                 5.3.28+dfsg2-2                        amd64        Berkeley v5.3 Database Libraries [runtime]
ii  libdb5.3++:amd64                               5.3.28+dfsg2-2                        amd64        Berkeley v5.3 Database Libraries for C++ [runtime]
ii  libdb5.3++-dev                                 5.3.28+dfsg2-2                        amd64        Berkeley v5.3 Database Libraries for C++ [development]
ii  libdb5.3-dev                                   5.3.28+dfsg2-2                        amd64        Berkeley v5.3 Database Libraries [development]

```

Seleccionamos la version de Bitcoin Core que queremos compilar

```
cd bitcoin
git checkout v26.0rc2
```

En caso de querer ver las versiones disponibles, podemos usar:
```
git tag -n | sort -V 
```

# Compilación de Bitcoin Core

Ya podemos pasar a la fase de preparación de la compilación:

```
./autogen.sh
./configure --with-incompatible-bdb --enable-suppress-external-warnings
```

Podemos iniciar la compilación usando el comando make. En caso de estar en Linux y disponer de una máquina con multiples CPUs podemos utilizar:


```
make -j "$(($(nproc) + 1))"
```

En cualquier otro caso usar simplemente el comando *make* sin parámetros:
```
make
```

Ahora toca esperar unos minutos en el mejor de los casos. Si intentas compilarlo en sistemas con recursos limitados (raspberry pi) puede tardar horas.

# ¿Que Sigue?

Está listo para ejecutar su primer test? Vaya a [Probando los tests Bitcoin Core](03_0_Testing-the-tests.md)

