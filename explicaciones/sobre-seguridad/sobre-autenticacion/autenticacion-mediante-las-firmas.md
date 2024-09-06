---
icon: signature-slash
---

# Autenticación mediante las firmas

Las solicitudes de cambios o escritura (POST) a Libro Mayor siempre contienen una carga (payload) diseñada para transmitir toda la información necesaria para realizar el cambio.

Debido a esto, los cambios son más directas, seguras y no se pueden repudiar.&#x20;

El mecanismo de seguridad principal para validar los cambios está contenido en el array `proofs` que se proporciona en el objeto `meta` como parte del mensaje.&#x20;

{% hint style="info" %}
Para lectura (GET) pueden usar autenticación con [`token JWT.` ](autenticacion-con-token-jwt.md)
{% endhint %}

Por ejemplo, el cuerpo de una solicitud para crear una Billetera se ve así:

```json
{
  "hash": "<hash de los datos>"
  "data": {
    "handle": "wallet-handle"
  },
  "meta": {
    "proofs": [{
      "method": "ed25519-v2",
      "public": "<clave pública>",
      "result": "<firma del hash>",
      "digest": "<hash de los datos>",
      "custom": {
        "moment": "2023-02-20T21:42:10.279Z"
      }
    }]
  }
}
```

Pasos para firmar el objeto como el anterior:

1. Serializar los datos
2. Hacer un hash de los datos serializados
3. Firmar el hash con una o más claves privadas

Las claves utilizadas para la firma serán utilizadas por el Libro Mayor para verificar si se permite ejecutar la solicitud en cuestión.

Pasos para verificar una mutación entrante:

1. Serializar los datos
2. Hacer un hash de los datos serializados
3. Comparar el hash recibido con el hash del payload
4. Verificar cada firma recibida utilizando las claves públicas del array de firmas y el hash calculado

Si los pasos anteriores son exitosos, significa que el payload recibido es válido y que fue enviado por los propietarios de las claves públicas proporcionadas. Aún necesitamos verificar los permisos de esas claves públicas para asegurarnos de que están autorizadas para realizar la operación requerida.&#x20;

Consulte [Acerca de la Autorización](../sobre-autorizacion.md) para más detalles

{% hint style="info" %}
Aunque las mutaciones se autentican mediante la firma del cuerpo, los clientes también pueden usar Tokens JWT para autenticarse y proporcionar una segunda capa de seguridad.
{% endhint %}
