---
icon: binary-circle-check
---

# Sobre autorización

Hay dos formas en las que los clientes pueden autenticarse en el Libro Mayor: la primera, firmando los cuerpos de las mutaciones, y la segunda, enviando un token JWT. Consulte [Acerca de la Autenticación](sobre-autenticacion/) para más detalles al respecto.

Estas dos formas de autenticación permiten a los usuarios decir quiénes son, pero no garantizan que tengan privilegios para acceder al Libro Mayor. Para asegurar que los usuarios tengan los privilegios necesarios para crear, actualizar y leer registros del Libro Mayor, se pueden definir reglas de control de acceso en tres niveles: servidor, Libro Mayor y registro. La capa de autorización verifica los permisos siguiendo la jerarquía:

```
record → ledger → server.
```

Una regla de acceso puede ser una regla simple o una referencia a una política. Consulte [Acerca de las Políticas de Seguridad](sobre-politicas-de-seguridad.md) para más detalles sobre las políticas.

Las reglas de permiso de acceso siguen el siguiente formato.

```tsx
type Rule: {
  /**
   * Define qué acción se asigna a la regla de acceso
   */
  action: AccessAction

  /**
   * Define a qué clase de registro del Libro Mayor se asigna la regla de acceso
   */
  record?: AccessRecord

  /**
   * Define las condiciones que el suscriptor del cuerpo de la solicitud 
   * debe seguir para conceder acceso.
   */
  signer?: AccessSigner

  /**
   * Define reclamaciones y metadatos sobre el token JWT para conceder acceso
   */
  bearer?: AccessBearer
}

type AccessPolicy: {
  /**
   * Adjunta una política a la regla de acceso
   */
   policy: string  
}

type AccessRule = Rule | AccessPolicy
```

Donde:

```tsx
type AccessAction: 
  'any'             | // Se aplica a todas las acciones siguientes
  'access'          | // Define permisos para acceder a registros hijos de un registro
  'create'          | // Define permisos para crear un registro
  'read'            | // Define permisos para leer un registro
  'drop'            | // Define permisos para eliminar un registro
  'update'          | // Define permisos para actualizar un registro
  'lookup'          | // Define permisos para buscar desde un registro

  'assign-signer'   | // Define permisos para asignar un firmante a un círculo
  'remove-signer'   | // Define permisos para desasignar un firmante de un círculo 

  'issue'           | // Define permisos para emitir un símbolo
  'destroy'         | // Define permisos para destruir un símbolo

  'spend'           | // Define permisos para gastar desde una billetera
  'limit'           | // Define permisos para limitar una billetera

  'commit'          | // Define permiso para confirmar una intención con una prueba
  'abort'             // Define permiso para abortar una intención con una prueba
```

> La acción `create` no es válida al definir acceso a un registro específico - reglas a nivel de registro - ya que el registro ya existe. Las reglas de acceso con la acción `create` se definen en registros padre, generalmente esto es a nivel de Libro Mayor o servidor.
>
> `access` tampoco es válido para registros sin hijos, debe definirse a nivel de servidor o Libro Mayor. Vea la sección sobre restricciones de acceso a registros hijos



```tsx
type AccessRecord: 
  'any'           | // Se aplica a todos los registros siguientes
  'server'        | // Define restricciones para acceder al servidor
  'ledger'        | // Define restricciones para realizar acciones en Libros Mayores
  'signer'        | // Define restricciones para realizar acciones en firmantes
  'symbol'        | // Define restricciones para realizar acciones en símbolos
  'wallet'        | // Define restricciones para realizar acciones en billeteras
  'intent'        | // Define restricciones para realizar acciones en intenciones
  'intent-proof'  | // Define restricciones para realizar acciones en pruebas de intención
  'effect'        | // Define restricciones para realizar acciones en efectos
  'bridge'        | // Define restricciones para realizar acciones en puentes
  'circle'        | // Define restricciones para realizar acciones en círculos
  'circle-signer' | // Define restricciones para realizar acciones en firmantes de círculo
  'policy'        | // Define restricciones para realizar acciones en políticas
  'schema'        | // Define restricciones para realizar acciones en esquemas
  'anchor'        | // Define restricciones para realizar acciones en anclajes
  'domain'          // Define restricciones para realizar acciones en dominios
```

> 💡 Las restricciones de acceso deben definirse respetando la jerarquía de servidor > Libro Mayor > registro, lo que significa:&#x20;
>
> * &#x20;La regla de registro `server` no se puede establecer en reglas a nivel de Libro Mayor o registro
> * La regla de registro `ledger` no se puede establecer en reglas a nivel de Libro Mayor o registro
> * La propiedad `record` debe omitirse o definirse con el mismo valor que el tipo de registro al definir el control de acceso a nivel de registro, es decir, no se permite definir reglas de acceso para gestionar `symbol` dentro de un registro `wallet`.
>
>
>
> 💡 La propiedad `record` puede omitirse al definir reglas de acceso, y en este caso indica que la regla de acceso está destinada a aplicarse al registro que define la regla de acceso

```tsx
type CircleConstraint = string
type CircleAggregation = {
  $in: Array<CircleConstraint>
}
type AccessCircle = CircleConstraint | CircleAggregation

enum RecordOwnership {
   Creator = 'creator'
}

type AccessSigner =  SignerConstraint | SignerAggregation
type SignerConstraint = {
  handle?: string // define restricciones para el handle del firmante
  format?: string // define restricciones para el formato del firmante
  public?: string // define restricciones para la clave pública del firmante
  $circle?: AccessCircle // define restricciones para los círculos del firmante
  schema?: string // define restricciones para el esquema del firmante
  $record?: RecordOwnership // define restricciones para la relación entre
                            // el firmante y el registro objetivo
  $ledger?: RecordOwnership // define restricciones para la relación entre
                            // el firmante y el Libro Mayor activo 
}
type SignerAggregation = {
  $in: Array<SignerConstraint> // la firma debe respetar al menos 
                           // una de las restricciones definidas en la lista 
}

/** 
 * @example 
 * Definición de reglas de agregación de firmantes que
 * requieren la firma del firmante 'owner' o 
 * un par de claves con la clave pública '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
 */

{ 
  ...,
  signer: {
     $in: [
	     { 
	       handle: 'owner', 
	     }, 
	     {
	       public: '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
	     }
	   ]
  }
}

/** 
 * @example 
 *  
 * Definición de una regla de firmante que requiere la firma 
 * de un firmante del círculo 'admin'
 */

{ 
  ...,
  signer: {
	  $circle: 'admin'
  }
}

/** 
 * @example 
 *  
 * Definición de una regla de firmante que requiere la firma 
 * del creador del registro
 */

{ 
  ...,
  signer: {
	  $record: 'creator'
  }
}

/** 
 * @example 
 *  
 * Definición de una regla de firmante que requiere la firma 
 * del creador del Libro Mayor
 */

{ 
  ...,
  signer: {
	  $ledger: 'creator'
  }
}
```

> 💡 La propiedad `signer` solo es válida para mutaciones, ya que una solicitud HTTP `GET` no tiene cuerpo.
>
> 💡 Dado que un registro puede contener múltiples firmas, estas reglas de acceso definen que al menos uno de los firmantes debe cumplir

```tsx
type AccessBearer = {
  /**
   * Define la regla de acceso con respecto al emisor del token portador
   *
   * @example company.org
   */
  iss?: string

  /**
   * Define la regla de acceso con respecto al sujeto del token portador
   *
   * @example admin
   */
  sub?: string

  /**
   * Define la regla de acceso con respecto a la audiencia del token portador
   *
   * @example ledger
   */
  aud?: string

  /**
   * Define si el hash de la solicitud es obligatorio
   *
   * @example true
   */
  hsh?: boolean

  /**
   * Define la clave requerida para verificar la firma del token portador
   *
   * @example WAweF9PHlboQoW0z8NqhZXFmzUTaV74NRFAd/aILprE=
   */
  $signer?: SignerConstraint
   
} | BearerAggregation

/** 
 * @example 
 *  
 * Definición de una regla de agregación de portador que
 * requiere que el token sea firmado por el firmante 'owner' o 
 * un par de claves con la clave pública '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
 */
{ 
  ...,
  bearer: {
     $in: [{
	 $signer: {
         handle: 'owner'
       }
     }, {
	 $signer: {
         public: '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
      }
     }]
  }
}

/** 
 * @example 
 *  
 * Definición de una regla de portador que requiere que el token sea firmado 
 * por un firmante del círculo 'admin'
 */
{ 
  ...,
  bearer: {
	  $signer: {
	  $circle: 'admin'
    }
  }
}
```

> 💡 Tanto **AccessSigner** como **AccessBearer** son objetos de comparación. Por favor, no los confunda con Registros Referenciados. Los objetos de comparación son más flexibles porque se puede comparar todo el objeto en lugar de solo el handle.

### Restricciones de acceso a registros hijos

Las restricciones de acceso a registros hijos funcionan como filtros y describen las condiciones mínimas requeridas para acceder a un registro hijo o a un registro. Se pueden establecer en cualquier registro que tenga registros hijos en el Libro Mayor. Los registros más comunes de este tipo son los registros `ledger` y `server`.

Estas restricciones de acceso se comprueban antes de validar la `action` real que el usuario quiere realizar, y sigue la jerarquía

```
servidor → Libro Mayor → registro (enfoque de arriba hacia abajo)
```

Ejemplos:

1. El `usuario A` quiere `leer` un `symbol`, por lo que primero debe cumplir los requisitos para acceder al `servidor` y luego cumplir los requisitos para acceder al `Libro Mayor`. Solo después de que se satisfagan esos requisitos, el Libro Mayor comprobará si el `usuario A` puede realizar la operación de `lectura` en el `symbol`.
2. El `usuario B` quiere `crear` un `Libro Mayor`, por lo que primero debe tener permisos para acceder al `servidor`. Después de eso, el Libro Mayor comprobará si el usuario tiene permisos para `crear` un `Libro Mayor`.

Ejemplos de reglas de acceso a registros hijos a nivel de servidor:

```tsx
/**
 * Define restricciones para acceder al servidor. Para acceder al servidor, el
 * usuario debe enviar un token firmado por la clave especificada.
 */
{
  action: 'access',
  bearer: {
     $signer: {
       public: '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
     }
  }
}

/**
 * Define restricciones para acceder a un Libro Mayor. Para acceder a un Libro Mayor, el
 * usuario debe enviar un token firmado por la clave especificada.
 */
{
  action: 'access',
  record: 'ledger',
  bearer: {
     $signer: {
       public: '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
     }
  }
}

/**
 * Define restricciones para acceder a cualquier registro. Para acceder a cualquier registro, el
 * usuario debe enviar un token firmado por la clave especificada.
 */
{
  action: 'access',
  record: 'any',
  bearer: {
     $signer: {
       public: '1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU='
     }
  }
}
```

Ejemplos de reglas de acceso a registros hijos a nivel de Libro Mayor:

```tsx
/**
 * Define restricciones para acceder a un Libro Mayor. Para acceder al Libro Mayor, el usuario
 * debe enviar un token firmado por cualquier firmante.
 */
{
  action: 'access',
  bearer: {
     $signer: {}
  }
}

/**
 * Define restricción para acceder a cualquier billetera. Para acceder a cualquier billetera, el usuario
 * debe enviar un token firmado por cualquier firmante.
 */
{
  action: 'access',
  record: 'wallet', 
  bearer: {
     $signer: {}
  }
}
```

### Reglas a nivel de servidor

Las reglas a nivel de servidor se definen a través de una variable de entorno llamada `SERVER_ACCESS_RULES` establecida en la API del Libro Mayor.

El acceso a cualquier registro y acción se puede definir a nivel de servidor ya que es el nivel raíz del control de acceso.

```tsx
tsxCopy/**
 * Reglas de acceso al servidor que restringen el acceso al servidor a usuarios con token firmado
 * y permiten a cualquier firmante crear instancias de Libro Mayor.
 */
 [
   {
      "action": "access",
      "bearer": {
         "$signer": {}
      }
   },
   {
      "action": "create",
      "record": "ledger",
      "signer": {}
   }
]
```

### Reglas a nivel de Libro Mayor

Las reglas a nivel de Libro Mayor se definen en la propiedad `access` al crear una instancia de Libro Mayor.

Se puede definir el acceso a cualquier registro a nivel de Libro Mayor, excepto para `server`.

```tsx
/**
 * Carga útil de un Libro Mayor que permite a cada
 * firmante realizar cualquier 
 * operación en cualquier registro del Libro Mayor.
 */
{
   "hash": "99dbff500c451a7480ce2dc5928875478cb47b2a358baeaf4064a60eadd897a5",
   "data": {
      "handle": "some_ledger",
      "signer": "some_signer",
      "access": [
         {
            "action": "any",
            "record": "any",
            "bearer": {
               "$signer": {}
            }
         }
      ]
   },
   "meta":{
      "proofs":[
         {
            "method": "ed25519-v2",
            "public": "1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU=",
            "result": "lPZsbs+BlWnu5Y5SMWH8AflAFzfKIvfvCgQ2dxZHC6D0j91N5o6F90hiWe6B8JV4MqSYsfGTzb9Rpfz8ecSbAg==",
            "digest": "3f294cf7533bf5c24675ad238fbfd235860fcc2bc02f8d666894726b7f4ce523",
            "custom": {
              "moment": "2023-02-20T21:42:10.279Z"
            }
         }
      ]
   }
}
```

### Reglas a nivel de registro

Las reglas a nivel de registro se definen en la propiedad `access` al crear un registro.

Se puede usar para definir el acceso para `leer` y `actualizar` el registro objetivo.

```tsx
tsxCopy/**
 * Carga útil de un firmante que puede ser actualizado y leído por el firmante que
 * creó el registro.
 */
{
   "hash":"914816628f3481e57a246d4906b90e8b0125fb0f508dd24b5f1a849545f2a5d1",
   "data":{
      "handle": "some_signer",
      "public": "1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU=",
      "format": "ed25519-raw",
      "access": [{
         "action": "read",
         "bearer": {
            "$signer": {
              public: "1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU="
            }
         }
      }, {
        "action": "update",
         "signer": "1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU=",
         "bearer": {
            "$signer": {
              public: "1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU="
            }
         }
      }]
   },
   "meta": {
      "proofs": [
         {
            "method": "ed25519-v2",
            "public": "1bZhhSgwDZ5C9pXsD2Q79A7rhxAZPBM3912G+lW/xIU=",
            "result": "lPZsbs+BlWnu5Y5SMWH8AflAFzfKIvfvCgQ2dxZHC6D0j91N5o6F90hiWe6B8JV4MqSYsfGTzb9Rpfz8ecSbAg==",
            "digest": "3f294cf7533bf5c24675ad238fbfd235860fcc2bc02f8d666894726b7f4ce523",
            "custom": {
              "moment": "2023-02-20T21:42:10.279Z"
            }
         }
      ]
   }
}
```
