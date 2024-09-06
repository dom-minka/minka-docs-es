# Preparación de débito

Las operaciones de débito generalmente ocurren cuando una intención de pago es iniciada por nosotros. También hay algunos casos de uso nuevos que se pueden admitir utilizando la operación de débito, como iniciación de pagos de terceros, flujo de solicitud y débitos directos.

En este tutorial nos centraremos solo en las intenciones de pago ya iniciadas por nosotros. Podemos verificar que hemos creado una intención entrante verificando las pruebas de esa intención. Sabemos que nuestra clave privada está almacenada de forma segura solo en nuestro sistema, por lo que no es posible que nadie más la use. Podemos verificar esto usando el `ledgerSdk`:

```tsx
await ledgerSdk.proofs
  .ledger() // Espera una firma del ledger
  .expect({ // Espera que el puente haya creado la intención
    custom: { status: 'created' },
    public: config.BRIDGE_PUBLIC_KEY,
  })
  .verify(context.intent)
```

> La iniciación de terceros se puede implementar de manera similar. Podemos tener una lista de claves públicas autorizadas y permitir débitos solo de intenciones de pago iniciadas por esas claves.

Es importante reservar los fondos del usuario en la fase de preparación de débito para evitar problemas con saldos insuficientes en la fase de confirmación. La principal diferencia entre créditos y débitos es que las transacciones ocurren en la fase de confirmación en el caso de los créditos, mientras que para los débitos ocurren en la fase de preparación.

Ahora pongamos todo esto junto con nuestras validaciones estándar del adaptador de crédito para obtener nuestro controlador de preparación de débito:

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
import { LedgerSdk, LedgerAddress } from '@minka/ledger-sdk'
import { config } from '../config'
import { bankSdk } from '../bank-sdk'

export class DebitBankAdapter extends IBankAdapter {
  async prepare(context: TransactionContext): Promise<PrepareResult> {		
		try {
			// Check that our bridge has created the intent
			await this.ledgerSdk.proofs
			  .ledger() // Expect a signature from ledger
			  .expect({ // Expect that bridge created the intent
			    custom: { status: 'created' },
			    public: config.BRIDGE_PUBLIC_KEY,
			  })
			  .verify(context.intent)
		} catch (e: Error) {
		  // Expected signatures not found, fail the operation
		  throw new EntryRejectedError(e.message)
		}
		
		const { entry } = context
		// In case of debits, source account is our user
		// Parse the source account info and verify
		const {
		  schema: accountType,
		  prefix: accountNumber,
		  domain: bank,
		} = LedgerAddress.parse(entry.source.handle)
		
		// This intent isn't related to our bank
		if (bank !== config.BRIDGE_DOMAIN) {
		  throw new EntryRejectedError('Invalid source domain.')
		}
		
		// We will only support transactional accounts for now
		if (accountType !== 'tran') {
		  throw new AccountNotFoundError(
		    'Only transactional accounts are supported.'
		  )
		}
		
		// We only support usd currency
		if (entry.symbol.handle !== 'usd') {
		  throw new UnsupportedSymbolError(
		    `Expected symbol usd, but got ${entry.symbol.handle}.`
		  )
		}
		
		// Check that account exists in our system and that it is active
		const account = bankSdk.getAccount(accountNumber)
		if (!account) {
		  throw new AccountNotFoundError(accountNumber)
		}
		
    if (!account.isActive()) {
      throw new AccountInactiveError('Account disabled.')
    }
		
		// Ledger sends amounts as integers, LedgerAmount can be
		// used to convert it to decimal taking into account the
		// number of decimal places (factor) of the symbol
    const decimalAmount = await LedgerAmount.toDecimal(
      entry.amount,
      entry.symbol.handle,
      this.ledgerSdk,
    )
		
		try {
	    const transaction = bankSdk.debit(
	      accountNumber,
	      decimalAmount,
	      `${context.entry.handle}-debit`,
	    )
			
      const account = coreSdk.getAccount(accountNumber)
      console.log(
        `Transaction successful, new balance: ${account.getBalance()}\n`
      )
		 
	    // All checks are successful, prepare the operation
	    return {
	      status: ResultStatus.Prepared,
		    coreId: `${config.BRIDGE_DOMAIN}.${transaction.id}`,
	    }
    } catch (e: Error) {
      // Basic error mapping of core errors to known ledger errors,
      // in real implementations this would cover all supported errors
      if (e instanceof CoreError && e.code === '101') {
        throw new InsufficientBalanceError(e.message, e.code)
      }
      
      throw new UnexpectedCoreError(e.message, e.code)
    }
  }
  
  async commit( ...
  }
  
  async abort( ...
  }
}
```

Para probar nuestro nuevo adaptador podemos crear una intención desde una cuenta que existe en nuestro sistema. Es importante usar nuestro puente firmador para crear esta intención, de lo contrario será rechazada por nuestro puente:

```
$ minka intent create
? Handle: nPoAYq5Ae1hljKpHO
? Action: transfer
? Source: tran:1001001002@mintbank.dev
? Target: tran:1001001424@teslabank.io
? Symbol: usd
? Amount: 5
? Add another action? No

Intent summary:
------------------------------------------------------------------------
Handle: nPoAYq5Ae1hljKpHO

Action: transfer
 - Source: tran:1001001002@mintbank.dev
 - Target: tran:1001001424@teslabank.io
 - Symbol: usd
 - Amount: $5.00

? Add custom data to this intent? No
? Signers: bridge@mintbank.dev
? Key password for bridge@mintbank.dev: *[hidden]*
? Sign this intent using signer bridge@mintbank.dev? Yes

Intent signed and sent to ledger ach
Intent status: pending
```

En la consola de nuestro puente deberíamos ver una solicitud de preparación de débito:

```bash
Restaring due to changes...

prepare-debit for intent nPoAYq5Ae1hljKpHO received:
------------------------------------------
 - Source: tran:1001001002@mintbank.dev
 - Amount: $5.00
 - Symbol: usd
------------------------------------------

Transaction successful, new balance: 5

Credit prepared, reply sent to ledger
Core Id: mint.3928459

commit-debit for intent YOaugmuJyq4qidUUj received:
------------------------------------------
 - Source: tran:1001001002@mintbank.dev
 - Amount: $5.00
 - Symbol: usd
------------------------------------------

Credit committed, reply sent to ledger
Core Id: <null>
```







