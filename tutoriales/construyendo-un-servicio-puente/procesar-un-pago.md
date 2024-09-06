# Procesar un Pago

### Creando un proyecto

La forma más rápida de comenzar a trabajar en una nueva integración es configurar un nuevo proyecto utilizando la CLI de Minka:

```markdown
$ minka bridge init bridge@mintbank.dev
? Handle (bridge@mintbank.dev): bridge@mintbank.dev
? Signer (bridge@mintbank.dev): bridge@mintbank.dev
? Signer password: *[hidden]*

Este comando va a inicializar un nuevo proyecto de puente con el handle 
bridge@mintbank.dev. Se va a crear un nuevo directorio mintbank.dev en tu directorio de trabajo actual. El firmante que seleccionaste se utiliza para firmar las solicitudes que el puente envía al ledger.

? ¿Quieres proceder? Sí

✔ Descargando el código del puente... Descargado
✔ Configurando el entorno... Configurado
✔ Instalando dependencias... Instalado

✅ Proyecto de puente creado con éxito
Se ha creado un nuevo proyecto NodeJS que implementa una interfaz de API compatible con el ledger. Aprende más sobre cómo comenzar a personalizar esta solución para adaptarla a tus necesidades leyendo el archivo **README.md** incluido.

Para ejecutar el puente creado, escribe lo siguiente:
 > cd mintbank.dev
 > minka bridge start 
```

Ahora hemos configurado un nuevo proyecto de puente, que es un gran punto de partida para construir integraciones entre redes de pago.

El proyecto ya utiliza muchas mejores prácticas para manejar complejidades como **procesamiento asincrónico**, **reintentos**, **idempotencia** y persiste todas las solicitudes para facilitar un proceso de **reconciliación** y **auditoría** fácil.

El servicio que hemos creado define todos los puntos finales de API necesarios para conectarse a un ledger remoto y una implementación simulada del núcleo bancario para demostrar cómo conectarse a tu propio núcleo bancario o sistema de pago.

Usar este código no es un requisito para conectarse al ledger, pero sí simplifica el proceso y resuelve muchos problemas adicionales como la reconciliación y la recuperación de errores. Estos y otros problemas suelen aparecer después de ir en vivo, lo que crea costos adicionales y resulta en una experiencia de usuario deficiente.

> El código del puente es de código abierto y puedes modificar cualquier parte de él para adaptarlo a tus propias necesidades.

### Ejecutando un puente local

Podemos ejecutar el servicio que hemos creado yendo al nuevo directorio y ejecutando los siguientes comandos:

```powershell
$ cd mintbank.dev
$ minka bridge start
Se ha detectado una configuración local del puente:
 - Handle: bridge@mintbank.dev
 - Ledger: ach
 - Runner: ts-node
 - Register with ledger: Yes

? ¿Ejecutar esta configuración? Sí

Comprobando dependencias:
✔ NodeJs... v20.11.1
✔ Docker... Running

Ledger en la nube detectado:
✔ Creando un túnel... Hecho

Iniciando servicios:
✔ Iniciando dependencias... Hecho
✔ Iniciando servicio principal... Hecho

El puente está disponible a través de las siguientes URLs:
 - Server URL: http://localhost:4042/v2
 - Public URL: https://modern-toes-check.loca.lt/v2

Registrando con el ledger:
✔ No se encontró el registro del puente, creándolo... Hecho

El puente se está ejecutando en el puerto 4042

✔ Buscando solicitudes no entregadas... Ninguna encontrada

Esperando nuevas solicitudes...
```

> La CLI inicia un servidor local y registra automáticamente el puente con el ledger creando o actualizando un registro de puente.

> Deja el puente en ejecución y continúa el tutorial en una nueva terminal.

Si seguiste los tutoriales anteriores, tu puente ya debería estar asignado a la billetera de tu banco. Si no es así, por favor consulta el tutorial [Integrando con un RTP](../orchestrando-flujos-usando-puentes/) para configurar esto.

Asignar un puente a una billetera declara al ledger que cada movimiento de saldo relacionado con esta billetera necesita **confirmación externa**.

Después de realizar este cambio, el ledger va a contactar a nuestro puente para confirmar cualquier débito o crédito relacionado con nuestra billetera del banco. Veremos cómo funciona esto en el siguiente capítulo.



### Procesando pagos

Ahora tenemos todo en funcionamiento y podemos intentar enviar una primera intención de pago a nuestro puente. Podemos desencadenar un crédito en nuestro puente local enviando fondos a nuestra billetera:

```powershell
$ minka intent create
? Handle: bSH5NIBW4vPoNvegx
? Action: transfer
? Source: svgs:1001001212@teslabank.io
? Target: tran:1001001234@**mintbank.dev**
? Symbol: usd
? Amount: 4
? Add another action? No

Intent summary:
------------------------------------------------------------------------
Handle: bSH5NIBW4vPoNvegx

Action: transfer
 - Source: svgs:1001001212@teslabank.io
 - Target: tran:1001001234@mintbank.dev
 - Symbol: usd
 - Amount: $4.00

? Add custom data to this intent? No
? Signers: teslabank
? Key password for teslabank: [hidden]
? Sign this intent using signer teslabank? Yes

Intent signed and sent to ledger ach
Intent status: pending
```

El ledger detecta que la billetera de destino de este movimiento de saldo tiene un puente asignado y envía una solicitud de **prepare credit** a ella. Podemos ver esto en el registro de nuestro puente:

```bash
Waiting for new requests...

prepare-credit for intent bSH5NIBW4vPoNvegx received:
------------------------------------------
 - Target: tran:1001001234@mintbank.dev
 - Amount: $4.00
 - Symbol: usd
------------------------------------------

Credit prepared, reply sent to ledger
Core Id: <null>

commit-credit for intent bSH5NIBW4vPoNvegx received:
------------------------------------------
 - Target: tran:1001001234@mintbank.dev
 - Amount: $4.00
 - Symbol: usd
------------------------------------------
Credit committed, reply sent to ledger
Core Id: mint.c390s216
```

> Como confirmación, el puente envía una firma al ledger que contiene un `coreId` (referencia de transacción) único de la operación realizada por el puente.

> El **payload completo de la intención de pago** también se entrega al puente para fines de verificación.

Esta solicitud es parte de un protocolo de dos fases de confirmación que el ledger utiliza para asegurarse de que todos los participantes en una transacción distribuida realizan correctamente sus responsabilidades. Las confirmaciones enviadas al ledger deben ser una **prueba** hecha con una clave privada registrada con el puente y contener una **referencia de transacción** de la operación realizada por el puente como evidencia.

Los errores se manejan de manera similar, se debe enviar una firma con detalles del error al ledger. En caso de que el puente esté inactivo, el ledger va a **reintentar** las solicitudes.}

> Cada solicitud enviada por el ledger tiene un id único que sirve como **token de idempotencia** para evitar operaciones dobles. Este id se envía en el campo `handle` de la solicitud entrante.

Exploraremos el protocolo de dos fases de confirmación en más detalle en los siguientes capítulos.
