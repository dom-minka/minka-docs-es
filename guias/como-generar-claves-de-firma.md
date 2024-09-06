---
icon: binary-lock
---

# Cómo generar claves de firma

Las solicitudes de cambios o escritura (POST) a Ledger siempre contienen objeto data diseñada para transmitir toda la información necesaria para realizar el cambio. Esta data esta encriptada con un llave privado generando un hash que permite verificar el author del cambio.&#x20;

El mecanismo de seguridad principal para validar los cambios está contenido en el array `proofs` que se proporciona en el objeto `meta` como parte del mensaje.&#x20;

Debido a esto, los cambios son más directas, seguras y no se pueden repudiar.&#x20;

{% hint style="info" %}
Mas sobre conceptos de [autenticación](../explicaciones/sobre-seguridad/sobre-autenticacion/autenticacion-mediante-las-firmas.md) mediante firmas.&#x20;
{% endhint %}

### Algoritmos de generar las firmas

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

### Pseudocódigo para proceso de generación de claves.

A continuación, se presenta un pseudocódigo que explica el proceso de generación de claves.&#x20;

Este pseudocódigo se enfoca en los pasos generales para transformar claves del formato `der` al formato `raw`.

```pseudo
pseudoCopy code

1. Importar la biblioteca de criptografía

2. Definir los prefijos DER para claves secretas y públicas
   - Prefijo de clave secreta (hexadecimal): '302e020100300506032b657004220420'
   - Prefijo de clave pública (hexadecimal): '302a300506032b6570032100'

3. Función para generar un par de claves Ed25519:
   - Generar un par de claves Ed25519 utilizando el módulo de criptografía.
   - Exportar la clave secreta en formato DER y convertirla a formato hexadecimal.
   - Exportar la clave pública en formato DER y convertirla a formato hexadecimal.

4. Eliminar los prefijos DER de las claves exportadas para obtener los valores en bruto.
   - Eliminar el prefijo secreto de la clave secreta DER.
   - Eliminar el prefijo público de la clave pública DER.

5. Codificar las claves en formato `raw` usando base64.
   - Convertir la clave secreta en bruto a base64.
   - Convertir la clave pública en bruto a base64.

6. Devolver las claves formateadas en `ed25519-raw` con las representaciones secretas y públicas.

7. Imprimir las claves generadas.
```

#### Pseudocódigo en Detalle:

```pseudo
pseudoCopy codeimportar biblioteca de criptografía

definir PREFIJO_SECRETO_DER = '302e020100300506032b657004220420'
definir PREFIJO_PUBLICO_DER = '302a300506032b6570032100'

función generarParDeClavesLedger():
    // Generar par de claves Ed25519
    parDeClavesDER = generarParDeClaves('ed25519')
    
    // Exportar claves en formato DER
    claveSecretaDER = exportarClaveSecreta(parDeClavesDER, formato='der', tipo='pkcs8')
    clavePublicaDER = exportarClavePublica(parDeClavesDER, formato='der', tipo='spki')
    
    // Convertir claves a hexadecimal y eliminar prefijos DER
    claveSecretaRAW = eliminarPrefijo(claveSecretaDER, PREFIJO_SECRETO_DER)
    clavePublicaRAW = eliminarPrefijo(clavePublicaDER, PREFIJO_PUBLICO_DER)
    
    // Codificar claves en base64
    claveSecretaCodificada = codificarBase64(claveSecretaRAW)
    clavePublicaCodificada = codificarBase64(clavePublicaRAW)
    
    retornar {
        formato: 'ed25519-raw',
        secreto: claveSecretaCodificada,
        publica: clavePublicaCodificada
    }

// Generar claves y mostrar
parDeClaves = generarParDeClavesLedger()
imprimir(parDeClaves)
```

Este pseudocódigo ilustra el flujo de generación de claves Ed25519 y su transformación al formato `raw`, detallando los pasos necesarios y haciendo énfasis en la eliminación de prefijos y la codificación en base64.

### Bibliotecas en otras lenguajes

Generar claves criptográficas en diferentes lenguajes de programación es un proceso bastante sencillo y directo que aporta enormes beneficios a la seguridad de cualquier sistema.

Al implementar criptografía adecuada para la generación de claves, como RSA, ECDSA o Ed25519, se mejora la protección de datos, se asegura la autenticidad y se evita el acceso no autorizado a la información.&#x20;

A continuación, se presentan algunas de las bibliotecas más utilizadas en Java y otros lenguajes para generar claves similares a las que se pueden crear en Node.js:

En **Java**, [Bouncy Castle](https://www.bouncycastle.org/) es una de las bibliotecas criptográficas más utilizadas. Soporta la generación de claves RSA, ECDSA, Ed25519, entre otros tipos. Otra opción es la [Java Security (JCA/JCE)](https://docs.oracle.com/javase/8/docs/technotes/guides/security/crypto/CryptoSpec.html), que es la arquitectura de criptografía estándar de Java y proporciona soporte para la generación de claves RSA, DSA y ECDSA, siendo parte integral de la plataforma Java. Para los que trabajan dentro del marco de Spring, [Spring Security](https://spring.io/projects/spring-security) puede ser útil, aunque utiliza las funcionalidades criptográficas nativas de Java. Además, Jose4j es una excelente opción para manejar JSON Web Key (JWK) y JSON Web Signature (JWS), compatible con claves RSA, EC y Octet.

En **Python**, [PyCryptodome](https://pycryptodome.readthedocs.io/) es una biblioteca robusta y de fácil integración que ofrece primitivas criptográficas de bajo nivel y soporta RSA, DSA, ECC y otros algoritmos. Otra opción popular es la biblioteca [Cryptography](https://cryptography.io/), conocida por su facilidad de uso y su amplia gama de soporte, incluyendo la generación de claves RSA, ECDSA y Ed25519.

En **Go (Golang)**, las bibliotecas estándar como crypto/rand y crypto/rsa permiten realizar operaciones criptográficas como la generación de claves RSA y ECDSA. Además, Go-ECDSA es un paquete específico para la gestión de claves ECDSA.

En **C#**, la versión de Bouncy Castle también está disponible y soporta algoritmos similares a los de la versión en Java. Otra biblioteca importante es [System.Security.Cryptography](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography?view=net-7.0), incluida en .NET, que facilita la generación de claves RSA, DSA y ECC de manera sencilla y segura.
