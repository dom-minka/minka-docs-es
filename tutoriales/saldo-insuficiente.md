# Saldo insuficiente

Para empezar, intentemos crear una solicitud que debería fallar porque no hay suficiente saldo en la cuenta.

```bash
as$ minka intent create
? Handle: huz1JQ-sWTxP1A_Lc
? Action: transfer
? Source: svgs:3@mintbank.dev
? Target: asvgs:1@mintbank.dev
? Symbol: usd
? Amount: 10
? Add another action? No
? Add custom data? No
? Signers: mint

Intent summary:
------------------------------------------------------------------------
Handle: huz1JQ-sWTxP1A_Lc

Action: transfer
 - Source: svgs:3@mintbank.dev
 - Target: asvgs:1@mintbank.dev
 - Symbol: usd
 - Amount: $10.00

? Sign this intent using signer mint? Yes

✅ Intent signed and sent to ledger sandbox
Intent status: pending
```

Vemos que obtenemos el error que esperábamos: `Saldo disponible insuficiente en la cuenta 3` y después de notificar al Libro Mayor del fallo, se llamó a **`POST /v2/debits/:handle/abort`** en el Bridge.

```
RECIBIDO POST /v2/debits
Error: Saldo disponible insuficiente en la cuenta 3
    at processPrepareDebit (file:///Users/branko/minka/demo-bridge-test/src/handlers/debits.js:69:13)
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
    at async prepareDebit (file:///Users/branko/minka/demo-bridge-test/src/handlers/debits.js:27:5)
    at async file:///Users/branko/minka/demo-bridge-test/src/middleware/errors.js:5:14
ENVIADA firma al Libro Mayor
{
  "handle": "deb_6uwDWMjA-vXeWsQ3k",
  "status": "failed",
  "coreId": "15",
  "reason": "bridge.unexpected-error",
  "detail": "Saldo disponible insuficiente en la cuenta 3",
  "moment": "2023-03-09T10:37:19.049Z"
}
RECIBIDO POST /v2/debits/deb_6uwDWMjA-vXeWsQ3k/abort
```

Ahora implementaremos este endpoint de la misma manera que los otros   (archivo **`src/adapters/debit.adapter.ts)`**&#x20;

```jsx
abort(context: TransactionContext): Promise<AbortResult> {
    console.log('RECIBIDO POST /v2/debits/abort')

    let result: AbortResult
    let transaction

    try {
      const extractedData = extractor.extractAndValidateData(context.entry)

      if (context.previous.job.state.result.status === ResultStatus.Prepared) {
        transaction = core.release(
          extractedData.address.account,
          extractedData.amount,
          `${context.entry.handle}-release`,
        )

        if (transaction.status !== 'COMPLETED') {
          throw new Error(transaction.errorReason)
        }
      }

      result = {
        status: ResultStatus.Aborted,
        coreId: transaction.id.toString(),
      }
    } catch (e) {
      result = {
        status: ResultStatus.Suspended,
      }
    }

    return Promise.resolve(result)
  }
```

Podemos ver que el procesamiento aquí depende del estado en el que ya estaba la Entrada. Si estaba `prepared`, necesitamos liberar los fondos, de lo contrario no tenemos que hacer nada.

Si ahora volvemos a ejecutar `Test 4`, obtendremos los siguientes registros, que es exactamente lo que esperábamos.

```
RECIBIDO POST /v2/debits
Error: Saldo disponible insuficiente en la cuenta 3
    at processPrepareDebit (file:///Users/branko/minka/demo-bridge-test/src/handlers/debits.js:69:13)
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
    at async prepareDebit (file:///Users/branko/minka/demo-bridge-test/src/handlers/debits.js:27:5)
    at async file:///Users/branko/minka/demo-bridge-test/src/middleware/errors.js:5:14
ENVIADA firma al Libro Mayor
{
  "handle": "deb_B2KIdu1X801Js3bof",
  "status": "failed",
  "coreId": "8",
  "reason": "bridge.unexpected-error",
  "detail": "Saldo disponible insuficiente en la cuenta 3",
  "moment": "2023-03-09T10:41:17.414Z"
}
RECIBIDO POST /v2/debits/deb_B2KIdu1X801Js3bof/abort
ENVIADA firma al Libro Mayor
{
  "handle": "deb_B2KIdu1X801Js3bof",
  "status": "aborted",
  "moment": "2023-03-09T10:41:17.783Z"
}
```
