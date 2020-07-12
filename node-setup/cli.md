# Interfaz de Línea de Comandos (CLI)

En tutorials anteriores, siempre hemos estado trabajando con el comando `cardano-node`,
pero cuando creamos el software del código fuente descrito [aquí](instalar.md),
en realidad también instalamos otros ejecutables, incluyendo la interfaz de línea de comandos (CLI) `cardano-cli`.

Esta interfaz de línea de comandos provee una colección de herramientas para la creación de llaves, construir transacciones, creación de certificados y otras operaciones importantes.

Está organizada en una jerarquía de subcomandos, y cada nivel traer su propia documentación pre-establecida de sintaxis y opciones de comando.

Podemos conseguir ayuda del nivel superior simplemente tecleando el siguiente comando sin argumentos:

        cardano-cli

Nos indicará que el único subcomando disponible es `shelley`, y tecleando

        cardano-cli shelley

nos mostrará los sub-subcomandos disponibles, uno de ellos es `node`. De esta manera podemos llegar al pie de la jerarquía:

        cardano-cli shelley node

y aprender del sub-sub-subcomando `key-gen`. Tecleando

        cardano-cli shelley node key-gen

nos informará sobre los parámetros que este comando utiliza, así podremos generar, por ejemplo, un _**key-pair**_ (par de llaves) de llaves offline y un archivo para el _**issue counter**_ (contador de emisión) tecleando

        cardano-cli shelley node key-gen \
            --cold-verification-key-file cold.vkey \
            --cold-signing-key-file cold.skey \
            --operational-certificate-issue-counter-file cold.counter

![`cardano-cli` command hierarchy](011_cli.png)
