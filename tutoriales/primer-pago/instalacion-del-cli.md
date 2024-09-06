# Instalación del CLI

Instala el CLI de Minka ejecutando el siguiente comando en tu terminal:

```bash
$ npm install -g @minka/cli
```

💡 Después de instalar la herramienta CLI, podrás interactuar con instancias de ledger locales o remotas usando el comando `minka`. Consulta todos los comandos disponibles escribiendo `minka --help`.

```
$ minka
Usage: minka [options] [command]

Options:
  --no-color            disable colored text (e.g. for test purposes)
  -V, --version         output the version number
  -v, --verbose         print more detailed output
  -ie, --inline-editor  do not open editor for inputs
  -t, --trace           prints even more details than verbose
  -h, --help            display help for command

Commands:
  server                connect to a server hosting ledgers
  ledger                connect to and manage ledger instances
  signer                manage local and remote signing keys
  wallet                manage wallets, ledger records that hold balances
  intent                create payment intents to transfer balances
  symbol                manage currencies available in ledger
  bridge                connect external integrations to ledger
  effect                observe and react to changes in ledger
  report                create and download reports
  schema                configure record fields validation rules
  domain                organize ledger records into namespaces
  circle                group signers to manage permissions more easily
  policy                configure ledger business rules
  anchor                link payment credentials to aliases
  layout                apply predefined ledger configurations
  system                advanced system monitoring and configuration
  help [command]        display help for command
```
