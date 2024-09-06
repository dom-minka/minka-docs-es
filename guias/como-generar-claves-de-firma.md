---
icon: binary-lock
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Cómo generar llaves y firmas

## Resumen

Las solicitudes de cambios o escritura (POST) a Ledger siempre contienen objeto data diseñada para transmitir toda la información necesaria para realizar el cambio. Esta data esta encriptada con un llave privado generando un hash que permite verificar el author del cambio.&#x20;

El mecanismo de seguridad principal para validar los cambios está contenido en el array `proofs` que se proporciona en el objeto `meta` como parte del mensaje.&#x20;

Debido a esto, los cambios son más directas, seguras y no se pueden repudiar.&#x20;

{% hint style="info" %}
Mas sobre conceptos de [autenticación](../explicaciones/sobre-seguridad/sobre-autenticacion/autenticacion-mediante-las-firmas.md) mediante firmas.&#x20;
{% endhint %}

## Pasos para generar y firmar

### Generación de Llaves Privadas

**Importar la biblioteca de criptografía**: Asegúrate de tener instalada una biblioteca que soporte la generación de claves Ed25519 (por ejemplo, `cryptography` en Python).

**Generar el par de claves Ed25519**: Utiliza la función específica de la biblioteca para generar un par de claves (pública y privada).

**Exportar las claves en formato raw y eliminar prefijos**: Convierte las claves generadas a formato raw, eliminas los prefijos específicos para obtener las claves en formato bruto, y luego codifica las claves en base64 para su uso.

### Uso de Claves Privadas para firmar un Hash de Datos

**Crear un hash de los datos**: Utiliza una función de hashing como SHA-256 para obtener un hash de los datos que quieres firmar.

**Firmar el hash con la clave privada**: Usa la clave privada Ed25519 para firmar el hash del objeto de datos, asegurando la autenticidad y la integridad de la información.

Estos pasos proporcionan un enfoque seguro para la generación y uso de claves criptográficas en la protección y autenticación de datos en un sistema de pagos.

## Algoritmos de generar las firmas

Estamos utilizando criptografía de curva elíptica estándar para crear llaves, verificar firmas y crear pruebas.

Esto te permite generar llaves de firmas utilizando cualquier biblioteca o herramienta disponible que soporte los algoritmos requeridos. Utilizamos **Ed25519 (EdDSA, Curve25519)** para generar nuestras llaves de firma, por lo que es algo a tener en cuenta al buscar bibliotecas para usar.

### **Recursos**

**Ed25519**: Es un algoritmo de firma digital basado en la curva elíptica Curve25519. Ed25519 es conocido por su alta seguridad, velocidad y tamaño compacto de las llaves. Puedes leer más sobre Ed25519 en la [RFC 8032](https://datatracker.ietf.org/doc/html/rfc8032). Características de Ed25519:

**Velocidad**: Ed25519 es rápido tanto en la generación de claves como en la firma y verificación.

**Seguridad**: Considerada resistente a los ataques avanzados, incluyendo los que aprovechan puntos débiles en curvas elípticas menos seguras.

**Compatibilidad**: Amplio soporte en bibliotecas de criptografía modernas.

**Curve25519**: Es una curva elíptica que proporciona un alto nivel de seguridad con eficiencia en la generación de claves y operaciones criptográficas. Más detalles sobre Curve25519 se encuentran en la [RFC 7748](https://datatracker.ietf.org/doc/html/rfc7748).

### **Formato de llaves**

Los llaves Ed25519 son, en realidad, números enteros muy grandes representados con 256 bits. Cuando estas claves se usan en transporte basado en texto, se codifican como texto.&#x20;

Existen diferentes formatos y codificaciones estándar para representar la clave en texto.

**Minka Ledger** utiliza el formato `raw` para representar las claves públicas y secretas de Ed25519, que se produce codificando el valor entero crudo de 256 bits con la codificación `base64`.

Las claves representadas en este formato tienen una longitud de 44 caracteres.

Ejemplo de clave pública:

```bash
tB/gTevBYDYYYUgOOlsKV2Iq8DzmEtleeTopaY63wqs=
```

{% hint style="info" %}
El formato \`raw\` es más pequeño en tamaño que las alternativas porque no incluye cabeceras adicionales. Además, muchas bibliotecas existentes esperan claves en formato \`raw\` de Ed25519, por lo que este formato también ofrece buena compatibilidad con el ecosistema actual. \</aside>
{% endhint %}

## Implementacion

### NodeJs

A partir de NodeJS (≥12.0.0), podemos generar estas claves utilizando el módulo integrado `crypto`. Anteriormente, teníamos que usar una biblioteca de terceros como `elliptic`.

Este módulo no puede exportar directamente claves Ed25519 en formato `raw`, que es el utilizado en el ledger, por lo que debemos depender del formato `der` compatible y transformarlo a nuestro formato `raw`.

La clave codificada en el formato binario `der` utiliza la sintaxis `ASN.1` y contiene los datos binarios prefijados con metadatos sobre la clave (identificador del algoritmo, longitud de la clave, etc.), seguidos por la clave binaria en bruto. Este prefijo es diferente para la clave secreta y la clave pública, como se muestra a continuación.

También existe una herramienta útil para depurar el contenido de registros en formato `der`, que comúnmente se utilizan para representar claves, certificados, firmas y otras entidades criptográficas:

* [https://lapo.it/asn1js/](https://lapo.it/asn1js/)

{% hint style="info" %}
Aunque esta herramienta no envía ningún dato al servidor al decodificar registros, ten cuidado: no se recomienda pegar claves privadas utilizadas en producción u otros datos sensibles.
{% endhint %}

Para obtener la clave **raw** utilizada por el ledger, simplemente necesitamos eliminar el prefijo `der` de la clave exportada desde el módulo `crypto` de NodeJS. Los prefijos exactos representados en codificación hexadecimal son:

* Prefijo hexadecimal de clave secreta: `302e020100300506032b657004220420`
* Prefijo hexadecimal de clave pública: `302a300506032b6570032100`

Después de eliminar estos prefijos de las exportaciones `der`, debemos codificar las claves en bruto de 256 bits restantes con `base64` para obtener una clave de 44 caracteres requerida para el ledger.

Así es como se genera un nuevo par de claves usando el paquete integrado `crypto`:

```jsx
const crypto = require('crypto')

const DER_SECRET_PREFIX = '302e020100300506032b657004220420'
const DER_PUBLIC_PREFIX = '302a300506032b6570032100'

function generateLedgerKeyPair() {
	// Generate random ed25519 keys
	const keyPairDer = crypto.generateKeyPairSync('ed25519')
	
	// Export secret and public keys in der format
	const secretDer = keyPairDer.privateKey
		.export({ format: 'der', type: 'pkcs8' })
		.toString('hex')
	const publicDer = keyPairDer.publicKey
		.export({ format: 'der', type: 'spki' })
		.toString('hex')
	
	const secretRaw = Buffer.from(
		secretDer.slice(DER_SECRET_PREFIX.length),
		'hex',
	).toString('base64')
	const publicRaw = Buffer.from(
		publicDer.slice(DER_PUBLIC_PREFIX.length),
		'hex',
	).toString('base64')

	return {
		format: 'ed25519-raw',
		secret: secretRaw,
		public: publicRaw,
	}
}

// Generate keys
const keyPair = generateLedgerKeyPair()

// Print keys
console.log(JSON.stringify(keyPair, null, 2))
```

### Bibliotecas en otras lenguajes

Generar claves criptográficas en diferentes lenguajes de programación es un proceso bastante sencillo y directo que aporta enormes beneficios a la seguridad de cualquier sistema.

Al implementar criptografía adecuada para la generación de claves, como RSA, ECDSA o Ed25519, se mejora la protección de datos, se asegura la autenticidad y se evita el acceso no autorizado a la información.&#x20;

A continuación, se presentan algunas de las bibliotecas más utilizadas en Java y otros lenguajes para generar claves similares a las que se pueden crear en Node.js:

En **Java**, [Bouncy Castle](https://www.bouncycastle.org/) es una de las bibliotecas criptográficas más utilizadas. Soporta la generación de claves RSA, ECDSA, Ed25519, entre otros tipos. Otra opción es la [Java Security (JCA/JCE)](https://docs.oracle.com/javase/8/docs/technotes/guides/security/crypto/CryptoSpec.html), que es la arquitectura de criptografía estándar de Java y proporciona soporte para la generación de claves RSA, DSA y ECDSA, siendo parte integral de la plataforma Java. Para los que trabajan dentro del marco de Spring, [Spring Security](https://spring.io/projects/spring-security) puede ser útil, aunque utiliza las funcionalidades criptográficas nativas de Java. Además, Jose4j es una excelente opción para manejar JSON Web Key (JWK) y JSON Web Signature (JWS), compatible con claves RSA, EC y Octet.

En **Python**, [PyCryptodome](https://pycryptodome.readthedocs.io/) es una biblioteca robusta y de fácil integración que ofrece primitivas criptográficas de bajo nivel y soporta RSA, DSA, ECC y otros algoritmos. Otra opción popular es la biblioteca [Cryptography](https://cryptography.io/), conocida por su facilidad de uso y su amplia gama de soporte, incluyendo la generación de claves RSA, ECDSA y Ed25519.

En **Go (Golang)**, las bibliotecas estándar como crypto/rand y crypto/rsa permiten realizar operaciones criptográficas como la generación de claves RSA y ECDSA. Además, Go-ECDSA es un paquete específico para la gestión de claves ECDSA.

En **C#**, la versión de Bouncy Castle también está disponible y soporta algoritmos similares a los de la versión en Java. Otra biblioteca importante es [System.Security.Cryptography](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography?view=net-7.0), incluida en .NET, que facilita la generación de claves RSA, DSA y ECC de manera sencilla y segura.
