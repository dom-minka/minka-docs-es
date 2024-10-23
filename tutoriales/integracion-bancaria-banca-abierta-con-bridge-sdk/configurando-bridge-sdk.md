# Configurando Bridge SDK

Para desarrollar nuestro servicio bridge, usaremos la biblioteca `@minka/bridge-sdk`. Utiliza una base de datos PostgreSQL, así que comencemos configurándola. Empecemos creando un archivo `docker-compose.yaml` con el siguiente contenido:

```yaml
version: '3.3'

volumes:
  postgres-bridge-volume:

services:
  postgres-bridge:
    restart: always
    image: postgres:15.1-alpine
    volumes:
      - postgres-bridge-volume:/var/lib/postgresql/data
    ports:
      - '5433:5432'
    environment:
      - POSTGRES_USER=bridge-service
      - POSTGRES_PASSWORD=bridge-service
      - POSTGRES_DB=bridge-service
```

Usaremos el siguiente comando para iniciar nuestra base de datos:

```bash
$ docker compose up
```

Para detenerlo, simplemente presiona Ctrl + C. Si quieres seguir usando la misma terminal, puedes ejecutar el mismo comando y agregar la bandera `-d` para desacoplar el contenedor de la terminal. En ese caso, para detener el contenedor, necesitarás ejecutar `docker compose down`. Los comandos de docker deben ejecutarse en el mismo directorio donde se encuentra `docker-compose.yaml`, de lo contrario, necesitarás usar el argumento `-f nombre_archivo`.

Ahora que tenemos la base de datos en funcionamiento, vamos a configurar un servicio bridge simple con endpoints predeterminados. Primero necesitaremos instalar las bibliotecas `@minka/bridge-sdk` y `@minka/ledger-sdk`.

```bash
$ npm install @minka/bridge-sdk
$ npm install @minka/ledger-sdk
```

Dado que la biblioteca se encarga de casi todo por nosotros, lo único que nos queda por hacer es configurarla. Lo haremos usando variables de entorno y la biblioteca `envalid`. Para facilitar las cosas, almacenaremos las variables de entorno en un archivo `.env` y usaremos la biblioteca `dotenv` para cargarlo. Primero instalemos las bibliotecas:

```bash
$ npm install envalid dotenv
```

Ahora crearemos el archivo `src/config.ts` y agregaremos el siguiente contenido:

```tsx
import { cleanEnv, host, num, port, str } from 'envalid'

export const config = cleanEnv(process.env, {
  BRIDGE_PUBLIC_KEY: str({ desc: 'Clave pública del Bridge' }),
  BRIDGE_SECRET_KEY: str({ desc: 'Clave privada del Bridge' }),
  LEDGER_HANDLE: str({ desc: 'Nombre del Ledger' }),
  LEDGER_SERVER: str({ desc: 'URL del Ledger' }),
  LEDGER_PUBLIC_KEY: str({ desc: 'Clave pública del Ledger' }),
  PORT: port({ default: 3000, desc: 'Puerto de escucha HTTP' }),
  TYPEORM_HOST: host({ desc: 'Host de conexión a la base de datos' }),
  TYPEORM_PORT: port({ desc: 'Puerto de conexión a la base de datos' }),
  TYPEORM_USERNAME: str({ desc: 'Nombre de usuario de conexión a la base de datos' }),
  TYPEORM_PASSWORD: str({ desc: 'Contraseña de conexión a la base de datos' }),
  TYPEORM_DATABASE: str({ desc: 'Nombre de la base de datos' }),
  TYPEORM_CONNECTION_LIMIT: num({
    default: 100,
    desc: 'Límite de conexiones a la base de datos',
  }),
})
```

También deberíamos crear el archivo `src/.env` con todas las variables de entorno necesarias:

<pre><code><strong>LEDGER_HANDLE=[nombre del ledger]
</strong>LEDGER_SERVER=[servidor donde se encuentra el ledger]
LEDGER_PUBLIC_KEY="" #SYSTEM stg
BRIDGE_PUBLIC_KEY="" 
BRIDGE_SECRET_KEY="" 
PORT=3001
TYPEORM_HOST=127.0.0.1
TYPEORM_PORT=5433
TYPEORM_USERNAME=bridge-service
TYPEORM_PASSWORD=bridge-service
TYPEORM_DATABASE=bridge-service
TYPEORM_CONNECTION_LIMIT=100
</code></pre>

para obtener la llave pública (`LEDGER_PUBLIC_KEY`) del ledger, ejecute:&#x20;

```
minka signer show system
```

Si usó el CLI, para generar la llave pública y privada del bridge, puede usar el siguiente comando para obtener la información (`BRIDGE_PUBLIC_KEY` y `BRIDGE_SECRET_KEY`)

```
minka signer show [signer] -s
```

Ahora podemos editar `src/main.ts` para configurar el Bridge SDK. El código carga la configuración para el servicio y la usa para construir el `ServerService` y `ProcessorService` de `@minka/bridge-sdk`. El `ServerService` creará todos los endpoints necesarios que necesitamos y aceptará solicitudes de procesamiento del Ledger. El `ProcessorService` procesará esas solicitudes y notificará al Ledger de los resultados.

```tsx
// cargar archivo .env
import dotenv from 'dotenv'
dotenv.config({ path: `${__dirname}/.env` })

// analizar y validar la configuración
import { config } from './config'

import {
  DataSourceOptions,
  LedgerClientOptions,
  ProcessorBuilder,
  ProcessorOptions,
  ServerBuilder,
  ServerOptions,
} from '@minka/bridge-sdk'

// importar nuestros adaptadores
import { SyncCreditBankAdapter } from './adapters/credit.adapter'
import { SyncDebitBankAdapter } from './adapters/debit.adapter'

import sleep from 'sleep-promise'

// configurar la configuración de la fuente de datos
const dataSource: DataSourceOptions = {
  host: config.TYPEORM_HOST,
  port: config.TYPEORM_PORT,
  database: config.TYPEORM_DATABASE,
  username: config.TYPEORM_USERNAME,
  password: config.TYPEORM_PASSWORD,
  connectionLimit: config.TYPEORM_CONNECTION_LIMIT,
  migrate: false,
}

// configurar la configuración del ledger
const ledger: LedgerClientOptions = {
  ledger: {
    handle: config.LEDGER_HANDLE,
    signer: {
      format: 'ed25519-raw',
      public: config.LEDGER_PUBLIC_KEY,
    },
  },
  server: config.LEDGER_SERVER,
  bridge: {
    signer: {
      format: 'ed25519-raw',
      public: config.BRIDGE_PUBLIC_KEY,
      secret: config.BRIDGE_SECRET_KEY,
    },
    handle: 'mint',
  },
}

// configurar el servidor para el servicio bridge
const bootstrapServer = async () => {
  const server = ServerBuilder.init()
    .useDataSource({ ...dataSource, migrate: true })
    .useLedger(ledger)
    .build()

  const options: ServerOptions = {
    port: config.PORT,
    routePrefix: 'v2',
  }

  await server.start(options)
}

// configurar el procesador para el servicio bridge
const bootstrapProcessor = async (handle: string) => {
  const processor = ProcessorBuilder.init()
    .useDataSource(dataSource)
    .useLedger(ledger)
    .useCreditAdapter(new SyncCreditBankAdapter())
    .useDebitAdapter(new SyncDebitBankAdapter())
    .build()

  const options: ProcessorOptions = {
    handle
  }

  await processor.start(options)
}

// configurar Bridge SDK
const boostrap = async () => {
  const processors = ['proc-0']

  await bootstrapServer(processors)

  // esperar a que se ejecuten las migraciones
  await sleep(2000)

  for (const handle of processors) {
    await bootstrapProcessor(handle)
  }
}

boostrap()
```

