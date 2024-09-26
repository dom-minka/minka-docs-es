# Como autenticar con JWT

El SDK de Ledger permite a los usuarios autenticarse en el ledger mediante el envío de tokens.

El objeto de opciones JWT (`JwtConfig`) tiene la siguiente definición



```typescript
type JwtConfig = {
  /**
   * Representa un identificador de cliente.
   *
   */
  iss: string

  /**
   * Representa un identificador de usuario del remitente del token.
   *
   */
  sub: string

  /**
   * Representa un destinatario para el cual está destinado un token, clave pública del ledger o handle
   *
   * */
  aud: string

  /**
   * Tiempo después del cual expira un token, segundos desde la época.
   *
   */
  exp: number

  /**
   * ID único del token, puede usarse para prevenir ataques de repetición
   *
   */
  jti?: string

  /**
   * Define si la reclamación de hash de solicitud (hsh) debe
   * ser creada y enviada.
   *
   * Se establece como "true" si este valor no
   * se proporciona.
   */
  createHsh?: boolean

  /**
   * Par de claves ED25519 para firmar el token
   *
   */
  keyPair: LedgerKeyPair

  /**
   * Identificador de la clave de verificación del token.
   * Acepta una clave pública o un handle de firmante del ledger.
   */
  kid?: LedgerHandle | LedgerKeyPair['public']
}
```

La configuración JWT se puede establecer y mezclar en tres niveles diferentes: SDK, cliente y solicitud.

### SDK - Asegurando la instancia del SDK

El SDK se puede asegurar al inicializar un nuevo objeto utilizando la propiedad `secure` de las opciones del constructor del SDK.

```typescript
import { LedgerSdk } from '@minka/ledger-sdk'

const sdk = new LedgerSdk({
  server: '<tu URL del ledger>',
  signer: {
	format: 'ed25519-raw',
	public: '<tu clave pública del ledger>'
	},
  secure: {
     aud: '<audiencia del token>',
     iss: '<emisor del token>',
     keyPair: {
       public: '<clave pública de firma>',
       format: '<formato de clave de firma>',
       secret: '<clave secreta de firma>'
     },
     sub: '<sub del token>',
     exp: 3600 // (1 hora)
     createHsh: true,
     kid: '<identificador de clave de verificación del token>',
  }
})
```

Esto también se puede establecer dinámicamente después de crear una nueva instancia con el método `setAuthParams`

```typescript
import { LedgerSdk } from '@minka/ledger-sdk'

const sdk = new LedgerSdk({
  server: '<tu URL del ledger>',
  signer: {
	format: 'ed25519-raw',
	public: '<tu clave pública del ledger>'
	}
})

sdk.setAuthParams({
  aud: '<audiencia del token>',
  iss: '<emisor del token>',
  keyPair: {
    public: '<clave pública de firma>',
    format: '<formato de clave de firma>',
    secret: '<clave secreta de firma>'
  },
  sub: '<sub del token>',
  exp: 3600 // (1 hora)
  createHsh: true,
  kid: '<identificador de clave de verificación del token>',
})
```

### Cliente - Asegurando el Cliente SDK

Un cliente puede ser asegurado dinámicamente con el método `setAuthParams`

```typescript
import { LedgerSdk } from '@minka/ledger-sdk'

const sdk = new LedgerSdk({
  server: '<tu URL del ledger>',
  signer: {
	format: 'ed25519-raw',
	public: '<tu clave pública del ledger>'
	}
})

// Asegurando el cliente de wallet
sdk.wallet.setAuthParams({
     aud: '<audiencia del token>',
     iss: '<emisor del token>',
     keyPair: {
       public: '<clave pública de firma>',
       format: '<formato de clave de firma>',
       secret: '<clave secreta de firma>'
     },
     sub: '<sub del token>',
     exp: 3600 // (1 hora)
     createHsh: true,
     kid: '<identificador de clave de verificación del token>',
})
```



### Solicitud - Asegurando una llamada a la API

Una solicitud puede ser asegurada dinámicamente con el método `setAuthParams`

```typescript
import { LedgerSdk } from '@minka/ledger-sdk'

const sdk = new LedgerSdk({
  server: '<tu URL del ledger>',
  signer: {
	format: 'ed25519-raw',
	public: '<tu clave pública del ledger>'
	}
})
	
// Asegurando una búsqueda de una wallet 
const { wallet } = await sdk.wallet.read('wallet-handle', {
	 aud: '<audiencia del token>',
   iss: '<emisor del token>',
   keyPair: {
     public: '<clave pública de firma>',
     format: '<formato de clave de firma>',
     secret: '<clave secreta de firma>'
   },
   sub: '<sub del token>',
   exp: 3600 // (1 hora)
   createHsh: true,
   kid: '<identificador de clave de verificación del token>',
})
```

