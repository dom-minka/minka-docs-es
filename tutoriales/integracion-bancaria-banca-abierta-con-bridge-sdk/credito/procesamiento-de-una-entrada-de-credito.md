# Procesamiento de una entrada de crédito

Ahora que estamos configurados, podemos crear una intención y deberíamos ver algunas solicitudes provenientes del Ledger. Aquí utilizaremos el firmante, la billetera y el puente que creamos en el tutorial anterior. Usaremos el mismo firmante **mint**, billetera y puente que creaste en el tutorial anterior y comenzaremos registrando el Puente con el Ledger. Ya debería existir, así que solo actualizaremos la URL para que apunte a nosotros. Sin embargo, antes de eso, necesitarás una URL pública a la que Ledger pueda apuntar. Si no tienes una, puedes usar ngrok

Comienza abriendo una terminal dedicada para ngrok y ejecutando el siguiente comando.



```bash
$ ngrok http 3001

ngrok (Ctrl+C to quit)

Check which logged users are accessing your tunnels in real time https://ngrok.com...

Session Status                online
Account                       <user account>
Version                       3.1.1
Region                        Europe (eu)
Latency                       32ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    **<bridge URL>** -> http://localhost

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

> 💡 Si no es la primera vez que ejecutas ngrok, es posible que tu URL haya cambiado, así que verifica porque es posible que necesites actualizarla de todos modos.

> ⚠️ Deja ngrok en ejecución y continúa el tutorial en una nueva terminal. Ahora deberías tener tres ventanas de terminal abiertas, una para el servicio Bridge, otra para ngrok y una nueva para los comandos CLI.

A continuación, ejecuta el siguiente comando, sustituyendo **mint** por tu propio puente. Cuando se te solicite, actualiza el campo `Server` y establécelo en del comando anterior.

```bash
$ minka bridge update mint

Bridge summary:
---------------------------------------------------------------------------
Handle: mint
Server: http://old.url:1234

Access rules:
#0
  - Action: any
  - Signer: 3wollw7xH061u4a+BZFvJknGeJcY1wKuhbWA3/0ritM=
#1
  - Action: any
  - Bearer:
    - sub: 3wollw7xH061u4a+BZFvJknGeJcY1wKuhbWA3/0ritM=

Updates:
------------------------------------------
? Select the field to update, select Finish to save changes. Server
? Server: <bridge URL>
? Select the field to update, select Finish to save changes. Finish
Updates received.
? Signer: mint

✅ Bridge updated successfully:
Handle: mint
Signer: 3wollw7xH061u4a+BZFvJknGeJcY1wKuhbWA3/0ritM= (mint)
```

Ahora finalmente crearemos una intención utilizando la herramienta CLI de esta manera.

```
$ minka intent create
? Handle: tfkPgUiuUjQjRB9KJ
? Action: transfer
? Source: account:73514@tesla
? Target: account:1@mint
? Symbol: usd
? Amount: 10
? Add another action? No
? Add custom data? No
? Signers: tesla
? Key password for tesla: *[hidden]*

Intent summary:
------------------------------------------------------------------------
Handle: tfkPgUiuUjQjRB9KJ

Action: transfer
 - Source: account:73514@tesla
 - Target: account:1@mint
 - Symbol: usd
 - Amount: $10.00

? Sign this intent using signer tesla? Yes

✅ Intent signed and sent to ledger sandbox
Intent status: pending
```

Deberíamos ver el primer registro de Entrada en los registros del Puente. En todos los métodos que estamos implementando, puedes registrar el `context` para ver más detalles o simplemente `context.entry` y `context.command` para ver las solicitudes.

```
RECIBIDO POST /v2/credits
```
