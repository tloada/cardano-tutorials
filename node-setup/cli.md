# Interfaz de Línea de Comandos (CLI)

En tutorials anteriores, siempre hemos estado trabajando con el comando `cardano-node`,
pero cuando creamos el software del código fuente descrito [aquí](instalar.md),
we actually installed other executables as well, including the command line interface `cardano-cli`.

This command line interface provides a collection of tools for key generation, transaction construction, certificate creation
and other important tasks.

It is organized in a hierarchy of subcommands, and each level comes with its own built-in documentation of command syntax and options.

We can get the top level help by simply typing the command without arguments:

        cardano-cli

We will be told that one available subcommand is `shelley`, and typing

        cardano-cli shelley

will display available sub-subcommands, one of which is `node`. We can continue drilling down the hierarchy:

        cardano-cli shelley node

and learn about the sub-sub-subcommand `key-gen`. Typing

        cardano-cli shelley node key-gen

will inform us about the parameters this command takes, so we can for example generate a key-pair of offline keys and a file for the issue counter
by typing

        cardano-cli shelley node key-gen \
            --cold-verification-key-file cold.vkey \
            --cold-signing-key-file cold.skey \
            --operational-certificate-issue-counter-file cold.counter

![`cardano-cli` command hierarchy](011_cli.png)
