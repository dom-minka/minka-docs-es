# Confirmación del crédito

### Confirmando créditos

A continuación, podemos implementar nuestra función de confirmación de crédito, que se llama cuando una intención de pago es confirmada por todos los participantes.

El estado de confirmación es final, cuando recibimos una llamada de confirmación de crédito sabemos que no puede ser revertida, por lo que es seguro liberar los fondos a nuestro usuario en este punto. El SDK del puente se asegurará de que esta confirmación de compromiso coincida con una preparación de débito que ya hemos realizado, lo que significa que no tenemos que hacer las mismas validaciones que hicimos en la llamada de preparación, y también podemos evitar muchas validaciones técnicas relacionadas con la consistencia de los datos.

Ahora agreguemos nuestro controlador de confirmación de crédito:

```tsx
import { nanoid } from 'nanoid'
import {
  AbortResult,
  CommitResult,
  IBankAdapter,
  ResultStatus,
  PrepareResult,
  TransactionContext,
  EntryRejectedError,
  AccountNotFoundError,
  UnsupportedSymbolError,
  AccountInactiveError,
} from '@minka/bridge-sdk'
import {
  LedgerSdk,
  LedgerAddress,
  LedgerAmount,
} from '@minka/ledger-sdk'
import { config } from '../config'
import { bankSdk } from '../bank-sdk'

export class CreditBankAdapter extends IBankAdapter {
  async prepare( ...
  }
  
  async commit(context: TransactionContext): Promise<CommitResult> {
    const { entry } = context
		 const { prefix: accountNumber } = Address.parse(entry.target.handle)
		 
		 // El ledger envía montos como enteros, LedgerAmount puede
		 // usarse para convertirlos a decimal teniendo en cuenta el
		 // número de lugares decimales (factor) del símbolo
		 const decimalAmount = await LedgerAmount.toDecimal(
		   entry.amount,
		   entry.symbol.handle,
		   this.ledgerSdk,
		 )
		 
	   const transaction = bankSdk.debit(
	     accountNumber,
	     decimalAmount,
	     `${context.entry.handle}-credit`,
	   )
	   
	   const account = coreSdk.getAccount(accountNumber)
	   console.log(
	     `Transaction successful, new balance: ${account.getBalance()}\n`
	   )
	   
	   return {
	     status: ResultStatus.Committed,
	     coreId: `${config.BRIDGE_DOMAIN}.${transaction.id}`,
	   }
  }
  
  async abort( ...
  }
}
```

> No tenemos ningún manejo especial de errores en esta implementación porque nuestro núcleo bancario simulado es muy simple y no debería ocurrir ningún error. El SDK del puente mapeará todos los errores no reconocidos a **`bridge.unexpected-error`** de forma predeterminada.
>
> **En una versión de producción de un puente, tendríamos que mapear nuestros errores internos a los errores del ledger correctamente.**

> La fase de confirmación no permite fallos, los participantes deben implementar un proceso para identificar cualquier operación fallida potencial y resolverla manualmente

Ahora podemos probar nuestro nuevo controlador creando una intención de pago exitosa:

```powershell
$ minka intent create

? Handle: YOaugmuJyq4qidUUj
? Action: transfer
? Source: svgs:1001001212@teslabank.io
? Target: tran:1001001001@mintbank.dev
? Symbol: usd
? Amount: 4
? Add another action? No

Intent summary:
------------------------------------------------------------------------
Handle: YOaugmuJyq4qidUUj
Action: transfer
 - Source: svgs:1001001212@teslabank.io
- Target: tran:1001001001@mintbank.dev
- Symbol: usd
- Amount: $4.00

? Add custom data to this intent? No
? Signers: teslabank
? Key password for teslabank: *[hidden]*
? Sign this intent using signer teslabank? Yes

Intent signed and sent to ledger ach
Intent status: pending

```

En la consola de nuestro puente deberíamos ver que se crea una nueva transacción:

```bash
Restaring due to changes...

prepare-credit for intent YOaugmuJyq4qidUUj received:
------------------------------------------
- Target: tran:1001001001@mintbank.dev
- Amount: $4.00
- Symbol: usd
------------------------------------------
Credit prepared, reply sent to ledger
Core Id: <null>

commit-credit for intent YOaugmuJyq4qidUUj received:
------------------------------------------
- Target: tran:1001001001@mintbank.dev
- Amount: $4.00
- Symbol: usd
------------------------------------------

Transaction successful, new balance: 74
Credit committed, reply sent to ledger
Core Id: mint.7294729
```
