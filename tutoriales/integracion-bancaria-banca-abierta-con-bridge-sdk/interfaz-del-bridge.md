# Interfaz del Bridge

Construiremos nuestro bridge en este tutorial agregando funcionalidad a medida que la necesitemos, paso a paso. Solo para tener una visión general de alto nivel de lo que obtendremos al final, aquí están todos los endpoints que implementaremos:

<table><thead><tr><th width="286">Endpoint</th><th>Solicitud</th><th>Descripción</th></tr></thead><tbody><tr><td><code>POST /v2/credits</code></td><td>prepare</td><td>Se llama durante el proceso de confirmación en dos fases cuando el Bridge necesita <strong>preparar una entrada de crédito</strong>. El Bridge debe verificar si la cuenta existe, está activa y realizar otras comprobaciones necesarias para asegurar que la intención de transferencia pueda proceder.</td></tr><tr><td><code>POST /v2/credits/:handle/commit</code></td><td>commit</td><td>Se llama durante el proceso de confirmación en dos fases cuando el Bridge necesita <strong>confirmar una entrada de crédito</strong>. Esta solicitud debe tener éxito y se considera exitosa incluso si hay un error durante el procesamiento. En ese caso, el Banco necesita corregir el problema manualmente.</td></tr><tr><td><code>POST /v2/credits/:handle/abort</code></td><td>abort</td><td>Se llama durante el proceso de confirmación en dos fases cuando el Bridge necesita <strong>abortar una entrada de crédito</strong>. Esta solicitud se llama si hay un problema durante el procesamiento de la transferencia. No puede fallar al igual que commit y debe revertir completamente la transferencia.</td></tr><tr><td><code>POST /v2/debits</code></td><td>prepare</td><td>Se llama durante el proceso de confirmación en dos fases cuando el Bridge necesita <strong>preparar una entrada de débito</strong>. Igual que para el crédito, pero también necesita retener o reservar los fondos necesarios en la cuenta de origen.</td></tr><tr><td><code>POST /v2/debits/:handle/commit</code></td><td>commit</td><td>Se llama durante el proceso de confirmación en dos fases cuando el Bridge necesita <strong>confirmar una entrada de débito</strong>. Igual que para el crédito.</td></tr><tr><td><code>POST /v2/debits/:handle/abort</code></td><td>abort</td><td>Se llama durante el proceso de confirmación en dos fases cuando el Bridge necesita <strong>abortar una entrada de débito</strong>. Igual que para el crédito, pero potencialmente también necesita liberar los fondos.</td></tr><tr><td><code>PUT /v2/intents/:handle</code></td><td>update</td><td>Se llama durante el proceso de confirmación en dos fases cuando el <strong>estado de la Intención se actualiza</strong>. Esto se puede usar para actualizar la Intención en la base de datos local para que el Banco sepa cuándo está completamente procesada.</td></tr></tbody></table>

Como puedes ver, el lado del Bridge de la interfaz Ledger - Bridge consiste en 3 rutas principales y 7 endpoints en total. Los exploraremos uno por uno en las siguientes secciones.

A continuación, vamos a crear dos adaptadores, contienen la única lógica personalizada que necesitamos implementar para conectarnos a nuestro núcleo. Vamos a crearlos y solo registrar las solicitudes entrantes por ahora.

```bash
$ mkdir adapters
```

Crear el archivo **`src/adapters/credit.adapter.ts` con el siguiente contenido**

```tsx
import {
  AbortResult,
  CommitResult,
  IBankAdapter,
  ResultStatus,
  PrepareResult,
  TransactionContext,
} from '@minka/bridge-sdk'
import { LedgerErrorReason } from '@minka/bridge-sdk/errors'

import * as extractor from '../extractor'
import core from '../core'

export class SyncCreditBankAdapter extends IBankAdapter {
  prepare(context: TransactionContext): Promise<PrepareResult> {
    console.log('RECIBIDO POST /v2/credits')

    let result: PrepareResult

    result = {
      status: ResultStatus.Prepared,
    }

    return Promise.resolve(result)
  }

  abort(context: TransactionContext): Promise<AbortResult> {
    console.log('RECIBIDO POST /v2/credits/abort')

    let result: AbortResult 

    result = {
      status: ResultStatus.Aborted,
    }

    return Promise.resolve(result)
  }

  commit(context: TransactionContext): Promise<CommitResult> {
    console.log('RECIBIDO POST /v2/credits/commit')

    let result: CommitResult

    result = {
      status: ResultStatus.Committed,
    }

    return Promise.resolve(result)
  }
  
  getEntry(context: TransactionContext):{schema:string,target:string, source:string, amount:number,symbol:string}{
    return {
        schema: context.entry.schema,
        target: context.entry.target!.handle,
        source: context.entry.source!.handle,
        amount: context.entry.amount,
        symbol: context.entry.symbol.handle
        }
}
}
```

Crear el archivo **`src/adapters/debit.adapter.ts` con el siguiente contenido**

```typescript
import {
  AbortResult,
  CommitResult,
  IBankAdapter,
  ResultStatus,
  PrepareResult,
  TransactionContext,
} from '@minka/bridge-sdk'
import { LedgerErrorReason } from '@minka/bridge-sdk/errors'

import * as extractor from '../extractor'
import core from '../core'

export class SyncDebitBankAdapter extends IBankAdapter {
  prepare(context: TransactionContext): Promise<PrepareResult> {
    console.log('RECIBIDO POST /v2/debits')

    let result: PrepareResult

    result = {
      status: ResultStatus.Prepared,
    }

    return Promise.resolve(result)
  }

  abort(context: TransactionContext): Promise<AbortResult> {
    console.log('RECIBIDO POST /v2/debits/abort')

    let result: AbortResult

    result = {
      status: ResultStatus.Aborted,
    }

    return Promise.resolve(result)
  }

  commit(context: TransactionContext): Promise<CommitResult> {
    console.log('RECIBIDO POST /v2/debits/commit')

    let result: CommitResult

    result = {
      status: ResultStatus.Committed,
    }

    return Promise.resolve(result)
  }
  
 getEntry(context: TransactionContext):{schema:string,target:string, source:string, amount:number,symbol:string}{
      return {
          schema: context.entry.schema,
          target: context.entry.target!.handle,
          source: context.entry.source!.handle,
          amount: context.entry.amount,
          symbol: context.entry.symbol.handle
          }
  }
}
```

Solo estamos registrando que la solicitud ocurrió y suspendiendo el procesamiento adicional. Construiremos los métodos uno por uno, sin embargo, antes de hacer eso necesitaremos un núcleo bancario simulado y algunas funciones auxiliares para extraer todos los datos que necesitamos de las solicitudes.

