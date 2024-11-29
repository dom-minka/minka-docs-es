# Generar JWT token

## Generación de JWT para Autenticación

### Descripción General

Para autenticar las peticiones al ledger, necesitamos generar un JSON Web Token (JWT) firmado. Este token contiene información importante sobre quién hace la petición y cuándo fue generada. El proceso de generación involucra varios pasos que aseguran la autenticidad y seguridad de las comunicaciones.

### Componentes del Token

#### El Header

El header del token contiene información sobre cómo se debe procesar. Utilizamos el algoritmo EdDSA (Edwards-curve Digital Signature Algorithm) para la firma, que es una variante moderna y segura de firma digital. También incluimos la clave pública (kid) que permitirá al servidor verificar la autenticidad del token.

```json
{
    "alg": "EdDSA",           // Algoritmo de firma
    "kid": "<public_key>"     // Clave pública para verificación
}
```

#### El Payload

El payload contiene la información principal del token. Incluimos varios campos timestamp y de identificación:

```json
{
    "iat": 1234567890,             // Momento de creación del token
    "exp": 1234571490,             // Momento en que el token expirará
    "iss": "bridge@teslabank.io",  // Quien emite el token (bridge)
    "aud": "ach",                  // El ledger que recibirá el token
    "sub": "teslabank.io"          // La entidad sobre la que trata el token
}
```

### El Proceso de Generación

#### Creación del Token Base

El primer paso es crear el token base. Este proceso comienza calculando los timestamps necesarios: el momento actual (iat) y cuando expirará el token (una hora después). Estos timestamps son cruciales para la seguridad, ya que limitan la ventana de tiempo en la que el token es válido.

```typescript
async function createJWT(): Promise<string> {
    const iat = Math.floor(moment().valueOf() / 1000)
    const exp = iat + 3600  // Token válido por 1 hora

    const jwtPayload = {
        iat,
        exp,
        iss: "bridge@teslabank.io", 
        aud: "ach",
        sub: "teslabank.io"
    }
    
    const token = await signJWT(
        jwtPayload,
        config.INTENT_PRIVATE_KEY,
        config.INTENT_PUBLIC_KEY
    );

    return `Bearer ${token}`
}
```

#### Proceso de Firma

La firma del token es crucial para su seguridad. Utilizamos la biblioteca 'jose' para manejar la firma EdDSA. El proceso incluye configurar el header protegido y firmar el contenido con nuestra clave privada. Esta firma garantiza que el token no ha sido modificado y que realmente fue creado por nosotros.

```typescript
async function signJWT(payload: any, secret: string, verificationKey: string): Promise<string> {
    return await new SignJWT(payload)
        .setProtectedHeader({ 
            alg: 'EdDSA',
            kid: config.INTENT_PUBLIC_KEY 
        })
        .sign(importPrivateKey(secret))    
}
```

#### Manejo de la Clave Privada

La clave privada requiere un formato específico para ser utilizada con Ed25519. Realizamos una serie de transformaciones para convertir la clave de base64 a un formato que el algoritmo pueda usar. Esto incluye añadir un prefijo ASN.1 específico y crear un objeto KeyObject con el formato correcto.

```typescript
function importPrivateKey(secret: string): crypto.KeyObject {
    const ASN1_PRIVATE_PREFIX = '302e020100300506032b657004220420';
    const keyHex = `${ASN1_PRIVATE_PREFIX}${Buffer.from(secret, 'base64').toString('hex')}`;
    return crypto.createPrivateKey({
        format: 'der',
        type: 'pkcs8',
        key: Buffer.from(keyHex, 'hex'),
    });
}
```

### Uso en las Peticiones

Una vez generado el token, se utiliza en las peticiones HTTP como un header de autorización. El token se prefija con la palabra "Bearer" siguiendo el estándar de autenticación. Además, se debe especificar el ledger con el que se está interactuando.

```typescript
const response = await axios.get(`${config.LEDGER_SERVER}/wallets`, {
    headers: {
        'Authorization': authorization,  // Bearer {token}
        'x-ledger': 'nombre del ledger'
    }
});
```

### Aspectos de Seguridad

La seguridad del sistema se basa en varios pilares:

1. **Tiempo de Vida Limitado**: Los tokens expiran después de una hora, limitando el impacto de un posible token comprometido.
2. **Firma Criptográfica**: El uso de EdDSA proporciona una firma criptográfica robusta y moderna.
3. **Protección de Claves**: La clave privada nunca se transmite, solo se usa para firmar localmente.

### Herramientas Necesarias

Para implementar este sistema de autenticación, necesitamos varias bibliotecas:

* `jose`: Maneja la creación y firma de JWTs
* `moment`: Gestiona los timestamps de manera confiable
* `crypto`: Proporciona las operaciones criptográficas necesarias
* `axios`: Realiza las peticiones HTTP al servidor
