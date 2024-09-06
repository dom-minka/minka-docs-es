# Iniciar un puente local

Todas las integraciones que conectan otros sistemas al libro mayor se denominan puentes. Estos suelen ser conexiones de dos vías, en un lado el puente se conecta al libro mayor y en el otro lado a un sistema externo. Los mensajes pueden fluir en ambas direcciones entre esos sistemas.

Minka CLI tiene un puente de demostración incluido en la herramienta. Este puente implementa el protocolo del libro mayor y funciona en modo interactivo. Imprime todos los mensajes recibidos y nos permite responder manualmente a ellos.

Podemos iniciar este puente ejecutando el siguiente comando:&#x20;

> Note que vamos a usar el firmante que acabamos de crear `bridge@mintbank.dev`

```powershell
$ minka bridge start
? Signer: bridge@mintbank.dev
? Signer password for bridge@mintbank.dev *[hidden]*
? Register with ledger? No

Cloud ledger detected, creating a tunnel...
Created ✔

Bridge is available through following URLs:
 - Server URL: http://localhost:4042/v2
 - Public URL: https://grumpy-llamas-share.loca.lt/v2

✅ Bridge is running on port 4042.
Press ctrl + c to exit.

Checking for undelivered requests...
None found ✔

Waiting for new requests... 
```

> Es importante responder a `Register with ledger` con `No`. Esto solo es necesario para el **primer registro**. Nuestro firmante local aún no tiene permisos en el libro mayor, por lo que esta operación fallaría. Lo haremos manualmente en el siguiente capítulo y otorgaremos los permisos requeridos.

