# Combinando créditos y débitos

Ahora estamos listos para jugar con nuestro nuevo Bridge 🥳. Para eso, tenemos 4 cuentas diferentes preconfiguradas en nuestro núcleo:

1 - sin saldo disponible porque es una cuenta nueva

2 - 70 USD disponibles

3 - sin saldo disponible porque todo está gastado o en retención

4 - cuenta inactiva

Intentemos enviar algo de dinero de la cuenta 2 a la cuenta 1. En realidad, probablemente procesaríamos esta transferencia internamente ya que es una transferencia intrabancaria, pero aquí por ahora será procesada por Ledger.

```bash
$ minka intent create
? Handle: knJOdFbP9K_WIr6ZN
? Action: transfer
? Source: svgs:2@mintbank.dev
? Target: svgs:1@mintbank.dev
? Symbol: usd
? Amount: 10
? Add another action? No
? Add custom data? No
? Signers: mint
? Key password for tesla: *[hidden]*

Intent summary:
------------------------------------------------------------------------
Handle: knJOdFbP9K_WIr6ZN

Action: transfer
 - Source: svgs:2@mintbank.dev
 - Target: svgs:1@mintbank.dev
 - Symbol: usd
 - Amount: $10.00

? Sign this intent using signer mint? Yes

✅ Intent signed and sent to ledger sandbox
Intent status: pending
```

Ahora deberíamos ver tanto las solicitudes de crédito como de débito en los registros, así:

```
RECIBIDO POST /v2/debits
ENVIADA firma a Ledger
{
  "handle": "deb_lWO5y5j6UrIctPqmZ",
  "status": "prepared",
  "coreId": "11",
  "moment": "2023-03-09T09:44:46.990Z"
}
RECIBIDO POST /v2/credits
ENVIADA firma a Ledger
{
  "handle": "cre_D1wJoSypAsF2XX_kW",
  "status": "prepared",
  "moment": "2023-03-09T09:44:47.512Z"
}
RECIBIDO POST /v2/debits/deb_lWO5y5j6UrIctPqmZ/commit
RECIBIDO POST /v2/credits/cre_D1wJoSypAsF2XX_kW/commit
ENVIADA firma a Ledger
{
  "handle": "deb_lWO5y5j6UrIctPqmZ",
  "status": "committed",
  "coreId": "13",
  "moment": "2023-03-09T09:44:47.908Z"
}
ENVIADA firma a Ledger
{
  "handle": "cre_D1wJoSypAsF2XX_kW",
  "status": "committed",
  "coreId": "14",
  "moment": "2023-03-09T09:44:47.919Z"
}

```
