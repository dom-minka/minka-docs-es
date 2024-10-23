# Entrada de Crédito

Comencemos actualizando la función de preparación de crédito. Necesitamos agregar el siguiente código (archivo **`src/adapters/credit.adapter.ts)`**

```typescript
// agrega estas importaciones
import { LedgerErrorReason } from '@minka/errors'

import * as extractor from '../extractor'
import core from '../core'  

  prepare(context: TransactionContext): Promise<PrepareResult> {
    console.log('RECIBIDO POST /v2/credits')

    let result: PrepareResult
      try {
        const extractedData = extractor.extractAndValidateData(this.getEntry(context))

      const coreAccount = core.getAccount(
        extractedData.address?.account,
      )
      coreAccount.assertIsActive()

      result = {
        status: ResultStatus.Prepared,
      }
    } catch (e: any) {
      result = {
        status: ResultStatus.Failed,
        error: {
          reason: LedgerErrorReason.BridgeUnexpectedCoreError,
          detail: e.message,
          failId: undefined,
        },
      }
    }

    return Promise.resolve(result)
  }
```

Ahora cuando ejecutemos `Test 1` de nuevo, deberíamos obtener:

```
RECIBIDO POST /v2/credits
ENVIADA firma al Ledger
{
  "handle": "cre_xzeDmFpyR5_GHWO35",
  "status": "prepared",
  "moment": "2023-03-09T08:39:54.932Z"
}
RECIBIDO POST /v2/credits/cre_xzeDmFpyR5_GHWO35/commit
```
