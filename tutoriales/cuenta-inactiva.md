# Cuenta inactiva

Finalmente, creemos una intención que debería fallar porque la cuenta de destino está inactiva.

```bash
$ minka intent create
? Handle: xwt2O_ONnGDsp8TtV
? Action: transfer
? Source: account:2@mint
? Target: account:4@mint
? Symbol: usd
? Amount: 10
? Add another action? No
? Add custom data? No
? Signers: mint

Intent summary:
------------------------------------------------------------------------
Handle: xwt2O_ONnGDsp8TtV

Action: transfer
 - Source: account:2@mint
 - Target: account:4@mint
 - Symbol: usd
 - Amount: $10.00

? Sign this intent using signer mint? Yes

✅ Intent signed and sent to ledger sandbox
Intent status: pending
```

Vemos que esta vez, debido a que el error ocurrió en el lado del crédito, recibimos dos solicitudes de aborto.

```
RECIBIDO POST /v2/debits
ENVIADA firma al Libro Mayor
{
  "handle": "deb_cvX9c-N4aDrB6DtsF",
  "status": "prepared",
  "coreId": "8",
  "moment": "2023-03-09T10:46:47.754Z"
}
RECIBIDO POST /v2/credits
InactiveAccountError: La cuenta 4 está inactiva
    at Account.assertIsActive (file:///Users/branko/minka/demo-bridge-test/src/core.js:101:13)
    at processPrepareCredit (file:///Users/branko/minka/demo-bridge-test/src/handlers/credits.js:73:17)
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
    at async prepareCredit (file:///Users/branko/minka/demo-bridge-test/src/handlers/credits.js:31:5)
    at async file:///Users/branko/minka/demo-bridge-test/src/middleware/errors.js:5:14 {
  code: '102'
}
ENVIADA firma al Libro Mayor
{
  "handle": "cre_KWS-5x837ecu5WdpO",
  "status": "failed",
  "reason": "bridge.unexpected-error",
  "detail": "La cuenta 4 está inactiva",
  "moment": "2023-03-09T10:46:48.121Z"
}
RECIBIDO POST /v2/debits/deb_cvX9c-N4aDrB6DtsF/abort
RECIBIDO POST /v2/credits/cre_KWS-5x837ecu5WdpO/abort
ENVIADA firma al Libro Mayor
{
  "handle": "deb_cvX9c-N4aDrB6DtsF",
  "status": "aborted",
  "coreId": "9",
  "moment": "2023-03-09T10:46:48.427Z"
}
```

Implementemos el aborto de crédito de la misma manera que lo hicimos para el débito, no necesitamos hacer mucho aquí.

```typescript
abort(context: TransactionContext): Promise<AbortResult> {
    console.log('RECIBIDO POST /v2/credits/abort')

    let result: AbortResult 

    result = {
      status: ResultStatus.Aborted,
    }

    return Promise.resolve(result)
  }
```

Nuevamente podemos notar que el único estado en el que podemos terminar es `aborted`. Si algo sale mal, terminaremos en el estado `suspended` y tendremos que arreglarlo internamente.

Y si ahora volvemos a intentar `Test 5`, vemos que está completamente abortado.

```
RECIBIDO POST /v2/debits
ENVIADA firma al Libro Mayor
{
  "handle": "deb_F-8HdIELqir6yp-g5",
  "status": "prepared",
  "coreId": "8",
  "moment": "2023-03-09T10:58:14.860Z"
}
RECIBIDO POST /v2/credits
InactiveAccountError: La cuenta 4 está inactiva
    at Account.assertIsActive (file:///Users/branko/minka/demo-bridge-test/src/core.js:101:13)
    at processPrepareCredit (file:///Users/branko/minka/demo-bridge-test/src/handlers/credits.js:73:17)
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
    at async prepareCredit (file:///Users/branko/minka/demo-bridge-test/src/handlers/credits.js:31:5)
    at async file:///Users/branko/minka/demo-bridge-test/src/middleware/errors.js:5:14 {
  code: '102'
}
ENVIADA firma al Libro Mayor
{
  "handle": "cre_bhu9PwG9QAbzRraqX",
  "status": "failed",
  "reason": "bridge.unexpected-error",
  "detail": "La cuenta 4 está inactiva",
  "moment": "2023-03-09T10:58:15.212Z"
}
RECIBIDO POST /v2/debits/deb_F-8HdIELqir6yp-g5/abort
RECIBIDO POST /v2/credits/cre_bhu9PwG9QAbzRraqX/abort
ENVIADA firma al Libro Mayor
{
  "handle": "deb_F-8HdIELqir6yp-g5",
  "status": "aborted",
  "coreId": "9",
  "moment": "2023-03-09T10:58:15.512Z"
}
ENVIADA firma al Libro Mayor
{
  "handle": "cre_bhu9PwG9QAbzRraqX",
  "status": "aborted",
  "moment": "2023-03-09T10:58:15.655Z"
}
```

Vemos que `prepare` falló porque `La cuenta 4 está inactiva`, por lo que tanto las Entradas de crédito como de débito recibieron las respectivas acciones de aborto y fueron abortadas.
