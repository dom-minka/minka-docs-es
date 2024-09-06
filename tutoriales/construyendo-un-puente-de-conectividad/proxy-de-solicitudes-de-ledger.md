# Proxy de solicitudes de ledger

El puente actúa como un conector bidireccional entre sistemas de pagos, tiene dos conjuntos de API para poder adaptar protocolos de ambos lados de la conexión.

El primero define los puntos finales de API utilizados por el ledger para entregar información a un banco u otro sistema financiero externo. Llamamos a estas API `externas`, ya que son utilizadas por sistemas externos para llamar a nuestros sistemas bancarios. Estas son las API de protocolo de dos fases que hemos estado trabajando en capítulos anteriores y algunos puntos finales adicionales para escuchar eventos opcionales de ledger.

El segundo contiene puntos finales de API que son consumidos por los sistemas bancarios internos para enviar información a ledger. Llamamos a estas API `internas`.

En este capítulo vamos a ver este segundo conjunto de API de puente. Estos puntos finales reflejan las API de ledger para permitir que los sistemas bancarios creen intenciones de pago, registren cuentas con ledger, consulten alias, etc.

Puede usar estos puntos finales para realizar validaciones de datos, verificar reglas de negocios y validar reglas de seguridad antes de enviar solicitudes a ledger. Un resumen de las API disponibles para los sistemas bancarios internos se encuentra a continuación:

*   POST /v2/intents

    Crea una nueva intención de pago en el ledger.
*   GET /v2/intents

    Obtiene intenciones de pago de ledger. Este punto final devuelve una lista de registros y admite filtrado.
*   GET /v2/intents/:handle

    Obtiene una sola intención de pago de ledger.
*   **POST** /v2/signers

    Crea un nuevo firmante en el ledger.
*   **PUT** /v2/signers/:handle

    Actualiza un firmante existente en el ledger.
*   **GET** /v2/signers

    Obtiene firmantes de ledger. Este punto final devuelve una lista de registros y admite filtrado.
*   **GET** /v2/signers/:handle

    Obtiene un solo firmante de ledger.
*   **POST** /v2/wallets

    Crea una nueva billetera en el ledger.
*   **PUT** /v2/wallets/:handle

    Actualiza una billetera existente en el ledger.
*   **GET** /v2/wallets

    Obtiene billeteras de ledger. Este punto final devuelve una lista de registros y admite filtrado.
*   **GET** /v2/wallets/:handle

    Obtiene una sola billetera de ledger.
*   **GET** /v2/wallets/:handle/balances

    Obtiene los saldos de la billetera de ledger. Cada billetera puede tener múltiples saldos, este punto final devuelve una lista de todos los saldos asignados a una billetera.

Los bancos también pueden llamar directamente a los puntos finales de ledger, estas API están aquí para centralizar toda la comunicación con ledger. Llamar a las API de ledger desde múltiples lugares significa que necesitamos aprovisionar y asegurar múltiples sistemas para mantener las claves privadas de ledger.

Los puntos finales de la API de puente anteriores están protegidos de forma predeterminada con una clave API que se genera aleatoriamente cuando se crea el proyecto.

Los usuarios pueden implementar estrategias de autenticación más avanzadas registrando funciones middleware de express. Esto nos permite integrar sistemas de autenticación existentes o implementar soluciones completamente nuevas fácilmente.

El constructor de servidor de puente acepta funciones middleware estándar de express que se pueden registrar antes de los controladores de ruta, por ejemplo, para registrar un middleware que verifique una clave API en todas las rutas internas podemos usar el siguiente código en `main.ts`:

```tsx
const server = ServerBuilder.init()
  .useDataSource({ ...dataSource, migrate: true })
  .useLedger(ledger)
  .internal.use(requireApiKey(config.API_KEY))
  .build()
```

> Una alternativa para asegurar los puntos finales de puente es configurar un proxy de autenticación delante del servicio de puente. Este servidor puede verificar y forzar todas las reglas de seguridad antes de llamar al puente

Registrar funciones middleware personalizadas nos permite adaptar completamente la solución de puente a nuestras necesidades. Por ejemplo, podemos debitar las cuentas de los usuarios inmediatamente al crear una nueva intención registrando un middleware en la ruta `/v2/intents` de la siguiente manera:

```tsx
const server = ServerBuilder.init()
  .useDataSource({ ...dataSource, migrate: true })
  .useLedger(ledger)
  .internal.post('/v2/intents', debitOnIntent)
  .build()
```

> La palabra clave `.internal` se utiliza para agregar controladores y funciones middleware a un enrutador que sirve puntos finales de API internos. `.external` se puede usar para hacer lo mismo para puntos finales de API externos, enfrentados a ledger.

Podemos probar las rutas internas de puente publicando un cuerpo de intención en nuestro puente usando cURL. Al hacer esto, necesitamos autenticarnos con una clave API y solo necesitamos publicar el cuerpo de la intención, sin pruebas. Si nuestra clave API se valida correctamente, el puente la completará firmándola y reenviándola a ledger.

Esto simplifica los modelos de interacción desde los servicios y aplicaciones bancarias internas al eliminar toda la complejidad relacionada con la criptografía y el manejo seguro de claves privadas.

Aquí hay un ejemplo de una intención enviada a ledger usando cURL:

```bash
curl \
  --header "Content-Type: application/json" \
  --header "X-API-Key: d9dkncow0f39d9c0cwkoc0kd" \
  --request POST \
  --data '{
	  "data": {
	    "handle": "H7vu2KgXCfNh9iNFnaWNg",
	    "claims": [
	      {
	        "action": "transfer",
	        "amount": 1,
	        "source": {
	          "handle": "svgs:1001001002@mintbank.dev"
	        },
	        "symbol": {
	          "handle": "usd"
	        },
	        "target": {
	          "handle": "tran:1001001234@teslabank.io"
	        }
	      }
	    ]
	  }
	}' \
  http://localhost:4042/v2/intents
```

> Como puede ver arriba, solo necesitamos enviar el contenido principal de una intención, la información sobre la fuente, el destino, el monto, etc.

En la consola de nuestro puente deberíamos ver una solicitud de preparación de débito:

```bash
Restaring due to changes...

prepare-debit for intent H7vu2KgXCfNh9iNFnaWNg received:
------------------------------------------
 - Source: tran:1001001002@mintbank.dev
 - Amount: $1.00
 - Symbol: usd
------------------------------------------

Transaction successful, new balance: 4

Credit prepared, reply sent to ledger
Core Id: mint.9371483

commit-debit for intent H7vu2KgXCfNh9iNFnaWNg received:
------------------------------------------
 - Source: tran:1001001002@mintbank.dev
 - Amount: $1.00
 - Symbol: usd
------------------------------------------

Credit committed, reply sent to ledger
Core Id: <null>
```

