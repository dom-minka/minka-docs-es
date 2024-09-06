---
icon: fingerprint
---

# Sobre autenticación

l protocolo de Minka se enfoca tanto en asegurar el mensaje como el canal de comunicación.

La seguridad del Libro Mayor se fundamenta en la [criptografía asimétrica](https://es.wikipedia.org/wiki/Criptograf%C3%ADa\_asim%C3%A9trica). Cada registro está protegido mediante un par de claves: una pública y una privada. Las [claves públicas](https://es.wikipedia.org/wiki/Clave\_p%C3%BAblica) se almacenan en el Libro Mayor, y cada operación se valida verificando las [firmas digitales](https://es.wikipedia.org/wiki/Firma\_digital) correspondientes.

La **criptografía asimétrica** se utiliza principalmente por su capacidad de proporcionar una comunicación segura, autenticación y verificación de firmas en un entorno no seguro.&#x20;

A diferencia de la criptografía simétrica, donde se utiliza una sola clave para encriptar y desencriptar, la criptografía asimétrica emplea un par de claves: una clave pública y una clave privada.

**Beneficios clave de la criptografía asimétrica:**

**Seguridad**: La clave pública puede ser compartida abiertamente mientras que la clave privada se mantiene segura. Esto asegura que solo el propietario de la clave privada pueda descifrar los mensajes cifrados con la clave pública o generar firmas válidas.

**Autenticación**: Permite verificar la identidad de los emisores de mensajes. Por ejemplo, una firma digital generada con la clave privada solo puede ser verificada con la correspondiente clave pública, garantizando la autenticidad del remitente.

**Integridad de los datos**: Ayuda a asegurar que los datos no hayan sido alterados durante la transmisión. Si los datos firmados no coinciden con la firma generada por la clave privada, se detecta inmediatamente cualquier modificación.

**No repudio**: Una vez que un mensaje está firmado digitalmente con una clave privada, el remitente no puede negar la autoría de ese mensaje, lo cual es esencial en transacciones financieras y legales.

Existen dos tipos principales de solicitudes en el Libro Mayor: mutaciones y lecturas. Las mutaciones son solicitudes que implican la creación o modificación de un registro en el Libro Mayor.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>Autenticación mediante las firmas</td><td></td><td></td><td><a href="autenticacion-mediante-las-firmas.md">autenticacion-mediante-las-firmas.md</a></td></tr><tr><td>Autenticación con token JWT</td><td></td><td></td><td><a href="autenticacion-con-token-jwt.md">autenticacion-con-token-jwt.md</a></td></tr></tbody></table>

Utilizando criptografía asimétrica, la plataforma garantiza la distribución de la seguridad a cada participante, proporcionando autenticación altamente segura, no repudio y reduciendo significativamente la posibilidad de una brecha de seguridad.

La plataforma no almacena llaves privadas y no puede suplantar a un participante, lo que simplifica las operaciones y disminuye el riesgo de comprometer la seguridad.

Consulte [Acerca de la Autorización](../sobre-autorizacion.md) para más detalles sobre la autorización.
