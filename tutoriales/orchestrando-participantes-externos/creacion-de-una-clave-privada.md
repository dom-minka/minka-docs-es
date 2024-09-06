# Creación de una clave privada

El modelo de seguridad del libro mayor se basa en claves digitales, un sistema criptográfico que nos permite verificar nuestra identidad y confirmar operaciones mediante el uso de una clave privada. Cada clave digital consta de dos partes: una clave pública y una privada. Tu clave privada nunca debe ser compartida con nadie. La clave pública puede compartirse de forma segura con otros participantes y se utiliza para confirmar que una operación fue realizada por nosotros.

> Necesitarás usar la herramienta CLI de Minka para este tutorial y necesitarás tener acceso a un libro mayor ACH. Sigue los tutoriales anteriores para registrarte, si aún no lo has hecho.

Las claves digitales se almacenan como registros de `signer` en el libro mayor. Cada firmante identifica un solo par de claves y puede contener información adicional sobre el propietario de la clave. Los firmantes nos permiten identificar a los participantes del sistema. Para comenzar a construir nuestra integración, primero crearemos un nuevo firmante que representará esta integración en el libro mayor. La forma más sencilla de hacer esto es utilizando la herramienta CLI de Minka:

> El handle es el identificador único, para este ejemplo se usó `bridge@mintbank.dev` pero para la ejecución de la guia debe usar bridge@`[nombre de la wallet]` que aparece en Studio&#x20;

```powershell
$ minka signer create
? Handle: bridge@mintbank.dev
? Key pair source: Generate new key pair
? Add custom data? No
? Signer password: *[hidden]*
? Repeat password: *[hidden]*

Signer bridge@mintbank.dev saved locally.
? Store to ledger? No

✅ Signer created successfully:
Handle: bridge@mintbank.dev
Public: rTM/+ly1Ytxu7ui9IYpuhA5HFDdJ+u/cQHQDkCttnTY=
Secret: *[value is hidden]*

⚠️  WARNING: Secret or private key is critical data that should be handled
with care. Private keys are used to modify balances and it is important to
understand that anyone who has access to that key can perform sensitive
ledger operations.
```

> Los algoritmos criptográficos utilizados por el libro mayor son estándares y documentados. Puedes usar cualquier herramienta o lenguaje de programación que admita estos algoritmos para crear tus claves. Minka CLI se utiliza aquí porque es la forma más fácil de comenzar, pero no es necesario usarla.

Puedes validar la creación del firmante con el siguiente comando

```powershell
$ minka signer show bridge@mintbank.dev

Signer summary:
---------------------------------------------------------------------------
Handle: bridge@mintbank.dev
Public: tUXm3F4dV0yfXbZJHHSPbxXDrRQBN7MkE8qyIs1zoAQ=
```
