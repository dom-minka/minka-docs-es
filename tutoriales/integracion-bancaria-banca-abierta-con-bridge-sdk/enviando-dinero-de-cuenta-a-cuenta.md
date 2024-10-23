# Enviando dinero de cuenta a cuenta

Configurando el servicio Bridge

El caso más simple de envío de dinero entre dos bancos es una transferencia directa de cuenta a cuenta.

Esto significa que conocemos tanto el número de cuenta de origen como el de destino y los bancos correspondientes. Una cuenta en este contexto representa cualquier cuenta cuyo saldo se gestiona fuera del ledger, también conocidas como cuentas externas.

Esto incluye cuentas corrientes y de ahorro, así como cuentas de préstamos y tarjetas de crédito. Por supuesto, otros tipos de productos también son posibles. Puedes encontrar más detalles en la documentación de referencia.

Ahora comenzaremos a implementar el componente Bridge que usaremos para comunicarnos con el Ledger. Por ahora, la iniciación del pago se realizará utilizando la herramienta CLI.

> 💡 Necesitarás tener los prerrequisitos `NodeJS` y `npm` instalados localmente.

> El código que vamos a generar puede obtenerlo desde el [repositorio](https://github.com/minkainc/demo-bridge-sdk/tree/master) público de Minka&#x20;

Primero, crearemos un proyecto nodeJS vacío con un directorio `src`.

```bash
$ mkdir demo-bridge
$ cd demo-bridge
$ mkdir src
$ npm init -y
```

Ahora que tenemos un proyecto node vacío, el siguiente paso es instalar `typescript` y `ts-node`.

Podemos hacerlo usando `npm`.

```bash
$ npm install -g typescript ts-node
```

Ahora inicializaremos el proyecto TypeScript:

```bash
$ tsc --init
```

> 💡 Ahora, deberías tener un archivo `tsconfig.json`, asegúrate de que el campo `target` sea `"es2018"`, si no, por favor actualízalo. Esta versión es necesaria para las expresiones regulares en `extractor.ts`

Ahora podemos probar si todo está configurado correctamente creando una simple ruta de hola mundo en `src/main.ts`.

```jsx
console.log(`Demo bank running`)
```

Para probar nuestro código necesitamos iniciar el servicio.

```bash
$ ts-node src/main.ts
Demo bank running
```

> 💡 Si quieres evitar tener que reiniciar el servicio después de cada cambio, puedes instalar una herramienta como `nodemon` que monitoreará tu proyecto en busca de cambios y reiniciará automáticamente el servicio.

Para instalar `nodemon` ejecuta:

```bash
$ npm install -g nodemon
```

Luego deberías ejecutar la aplicación usando:

```bash
$ nodemon src/main.ts
```
