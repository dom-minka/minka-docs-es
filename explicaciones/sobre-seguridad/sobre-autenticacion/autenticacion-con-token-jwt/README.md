---
icon: binary-lock
---

# Autenticación con token JWT

### Tokens JWT

{% hint style="info" %}
Este mecanismo funciona para solicitudes de lectura, ya que estas suelen ser solicitudes HTTP `GET` sin cuerpo.&#x20;
{% endhint %}

Para estas solicitudes, la URL, los parámetros de consulta y los encabezados definen lo que va a devolver la API.&#x20;

Para hacer que la API del Libro Mayor sea fácil de usar, pero manteniendo el mismo modelo de seguridad, el Libro Mayor admite tokens JWT para el intercambio de contexto de seguridad entre clientes y el servidor.&#x20;

Los tokens JWT son muy flexibles y también permiten a los usuarios transportar información adicional como parte de su carga útil que puede ser verificada por el Libro Mayor. Para mantener el modelo de seguridad primario compatible con el modelo de firmas de cuerpo, los clientes que poseen claves privadas pueden emitir tokens JWT que pueden ser validados por claves públicas a las que el Libro Mayor tiene acceso.

La presencia de un token - encabezado de Autorización - no es obligatoria. Se vuelve necesaria a través de la configuración de reglas de acceso de autorización que requieren un token para conceder acceso. Una vez enviado, el token se valida en cuanto a su formato, firma y expiración, independientemente de la presencia de reglas de acceso.

En este modelo, el token JWT reemplaza el cuerpo que se envía en las solicitudes de mutación, pero toda la seguridad sigue siendo la misma. Un cliente emite un JWT y lo firma con su propia clave privada. El Libro Mayor puede verificar ese JWT con una clave pública y aplicar las restricciones de seguridad configuradas para esa clave pública en caso de que la verificación sea exitosa. Las solicitudes con tokens inválidos son rechazadas independientemente de las reglas de autorización establecidas en el Libro Mayor.

### Envio de Token

El JWT debe enviarse al Libro Mayor de manera estándar, utilizando el encabezado `Authorization`:

```
Authorization: Bearer <JWT token>
```

JWT required headers:

```
kid - clave pública que debe usarse para verificar la firma del token
alg - algoritmo utilizado para firmar el Token JWT
```

{% hint style="info" %}
Algoritmos soportados en el momento actual: `EdDSA (ed25519 schema)`
{% endhint %}

Definición de claims del payload JWT:

```
iss - emisor del token, representa un identificador de cliente, por ejemplo `cli`,
      `studio`, etc.

sub - asunto del token, representa un identificador de usuario, ya sea la clave pública o el handle del firmante o una cadena arbitraria para tokens externos.

aud - audiencia del token, representa un destinatario para el cual está destinado un token

iat - tiempo de emisión, tiempo en el que se emitió un token; segundos

exp - tiempo de expiración, tiempo después del cual expira un token; segundos

jti - (opcional) id único del token, puede usarse para prevenir ataques de repetición

hsh - (opcional) hash de la solicitud (sha256), campo personalizado del Libro Mayor que permite la validación del contenido de la solicitud
```

Todos los claims listados arriba excepto `jti` y `hsh` son requeridos.

&#x20;Las claves públicas o handles pueden usarse para todas las entidades que los tienen como identificadores. Los handles son preferibles, si una clave está registrada en el Libro Mayor, ya que son más cortos.

Los tokens sin `hsh` no están vinculados a una solicitud específica y pueden usarse para múltiples solicitudes de API. La expiración del token está controlada por el claim `exp`. Si un claim `jti` está presente, el Libro Mayor necesita almacenar el id del token hasta que expire el tiempo de expiración del token. Los clientes deben crear tokens de corta duración si proporcionan un `jti`, el `exp` máximo permitido es de 5 minutos para tokens de un solo uso.

También se puede crear un token más seguro incluyendo un hash de solicitud. Esto vincula un token a una solicitud específica, por lo que limita los posibles ataques en caso de que se filtre un token. El algoritmo de hash utilizado es el mismo que para el cuerpo de la solicitud descrito anteriormente. Los pasos para crear un hash de solicitud:

1. Crear un objeto JSON que represente una solicitud

```json
jsonCopy{
  url: "<URL absoluta de la solicitud, incluyendo parámetros de consulta>",
  method: "<método HTTP, en mayúsculas>", // por ejemplo POST
  headers: {
    // Cualquier encabezado protegido o null si no se deben proteger encabezados
    // pares clave/valor, las claves deben estar en minúsculas,
    // por ejemplo "content-type": "application/json"
  },
  body: {
    // Cuerpo de la solicitud o null. Esto debe ser un objeto en caso de JSON.
  }
}
```

2. Serializar el objeto JSON de la solicitud a una cadena utilizando un algoritmo determinista - dos objetos con los mismos nombres y valores de propiedad deben resultar en la misma cadena. Por ejemplo: la cadena resultante debe ser igual para los siguientes objetos de solicitud.

```tsx
tsxCopyconst aRequest = {
  url: "https://minka.io",
  method: "GET" 
}

const anotherRequest = {
  method: "GET"
  method: "https://minka.io" 
}
```

3. Hacer un hash del objeto de solicitud serializado con el algoritmo `sha256`.
4.  Formatear el campo `hsh` utilizando el siguiente formato

    ```json
    jsonCopy"hsh": "<valor del hash>:<encabezados protegidos, separados por comas>"

    // ejemplo, si los encabezados Content-Type y X-Api-Key están protegidos
    "hsh": "3da5df75f03a365b0bc4f53946c77f017aa4fd03ba49977fb7ceb8d75f65cb8f:content-type,x-api-key"

    // ejemplo, si no hay encabezados protegidos, solo se debe incluir el hash
    "hsh": "3da5df75f03a365b0bc4f53946c77f017aa4fd03ba49977fb7ceb8d75f65cb8f"
    ```

Aún necesitamos verificar los permisos de esas claves públicas para asegurarnos de que están autorizadas para realizar la operación requerida.

Consulte [Acerca de la Autorización](../../sobre-autorizacion.md) para más detalles sobre la autorización.
