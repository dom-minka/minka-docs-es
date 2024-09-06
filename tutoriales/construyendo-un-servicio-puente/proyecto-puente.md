# Proyecto Puente

## Estructura del proyecto del puente

El SDK del puente resuelve por nosotros la programación, la comunicación con el ledger, la persistencia de datos, la idempotencia, la auditoría y los reintentos. Solo necesitamos implementar adaptadores para realizar las operaciones de dos fases de confirmación en los sistemas bancarios.

La mayor parte de la complejidad relacionada con la comunicación con el ledger es manejada por las bibliotecas `@minka/bridge-sdk` y `@minka/ledger-sdk` que son proporcionadas y mantenidas por el equipo central del ledger. Estas bibliotecas ya están instaladas y configuradas en nuestro proyecto.

La estructura de archivos del proyecto se muestra a continuación:

```markdown
|- src
|  |- adapters
|  |  |- credit.adapter.ts
|  |  |- debit.adapter.ts
|  |  `- README.md
|  |
|  |- bank-sdk
|  |  |- errors.ts
|  |  |- account.ts
|  |  |- transaction.ts
|  |  |- ledger.ts
|  |  |- index.ts
|  |  `- README.md
|  |
|  |- .env
|  |- .example.env
|  |- config.ts
|  `- main.ts
|
|- .gitignore
|- package-lock.json
|- package.json
|- tsconfig.json
`- README.md
```

El proyecto es muy simple porque solo contiene código que es específico de nuestra integración, todo lo que es reutilizable y genérico ya está incluido en las bibliotecas proporcionadas por el equipo del ledger.

### Adaptadores del puente

La mayor parte del trabajo que necesitamos hacer está relacionado con el directorio `adapters`. Este directorio contiene adaptadores personalizados que mapean las operaciones de dos fases de confirmación a los sistemas bancarios. Como puedes ver, este directorio contiene dos archivos, uno para créditos y otro para débitos.

Cada adaptador tiene tres funciones, `prepare`, `commit` y `abort`, que podemos usar para ejecutar las validaciones necesarias y crear las transacciones requeridas en los sistemas bancarios centrales como respuesta a las intenciones de pago del ledger. Estas funciones solo necesitan devolver el resultado final de la operación, ¿fue exitosa o no, y el `bridge-sdk` va a enviar todas las pruebas necesarias al ledger para registrar correctamente este resultado.

Solo necesitamos implementar esos dos archivos y tendremos una integración funcional con el ledger. También hay un archivo `README.md` en este directorio. Este archivo contiene una explicación más detallada relacionada con los adaptadores

> Agregar archivos `README.md` es un patrón general que el proyecto sigue para tener las respuestas más comunes disponibles junto con el código.

### SDK del banco

El directorio `bank-sdk` contiene una implementación simulada de un núcleo bancario en memoria. Este código se agrega al proyecto de forma predeterminada para mostrar cómo conectarse a un sistema bancario central.

Puedes eliminar este código del proyecto y reemplazarlo con SDKs que se conecten a tus sistemas bancarios. Solo se agrega para demostración.

### Configuración

El archivo `.env` se genera automáticamente por la CLI al configurar el proyecto. Utiliza valores predeterminados para la conexión a la base de datos, firmantes proporcionados a la CLI al crear el proyecto, etc.

El archivo `config.ts` carga y valida el archivo `.env`. Por favor, revisa este archivo de configuración y actualiza los valores según sea necesario. Protege las claves privadas y las contraseñas de la base de datos utilizando las mejores prácticas que normalmente sigues al implementar servicios dentro de tu organización.

> El archivo `.env` contiene datos sensibles como claves y contraseñas y nunca debe ser confirmado en el control de versiones

El archivo `main.ts` es el punto de entrada de nuestro servicio de puente. Este archivo inicializa todo el servicio y lo inicia. Usa este archivo para registrar adaptadores adicionales, rutas y modificar cualquier otro valor de configuración que puedas necesitar cambiar.

El servicio consta de dos componentes principales que se inician en `main.ts`, estos son un `server` y un `processor`.

El servidor es una aplicación express que expone APIs REST, puedes configurar el servidor de la siguiente manera:

```tsx
const server = ServerBuilder.init()
  .useDataSource({ ...dataSource, migrate: true })
  .useLedger(ledger)
  .build()

const options: ServerOptions = {
  port: config.PORT,
  routePrefix: 'v2',
}

await server.start(options)
```

Los procesadores son trabajadores en segundo plano que habilitan un modelo de procesamiento asincrónico. Por ejemplo, los créditos y los débitos son operaciones asincrónicas, por lo que los adaptadores para esas operaciones se pueden registrar al iniciar los procesadores:

```tsx
const processor = ProcessorBuilder.init()
  .useDataSource(dataSource)
  .useLedger(ledger)
  .useCreditAdapter(new CreditBankAdapter())
  .useDebitAdapter(new DebitBankAdapter())
  .build()

const options: ProcessorOptions = {
  handle,
}

await processor.start(options)
```

> Puedes aprender más sobre la arquitectura y la configuración del puente en el archivo `README.md` del proyecto.
