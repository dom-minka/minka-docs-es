---
icon: key
---

# Errores de integridad criptográfica

Estos errores están relacionados con la integridad de entidades criptográficas: hashes, firmas y claves.

La validación que produce estos errores ocurre durante el procesamiento de solicitudes de API, por lo que también tienen códigos de estado HTTP de respuesta asociados.

| Status | Reason                           | Detail                                                                                           |
| ------ | -------------------------------- | ------------------------------------------------------------------------------------------------ |
| `422`  | **`crypto.hash-invalid`**        | El hash del registro recibido no coincide con el registro.                                       |
| `422`  | **`crypto.parent-hash-invalid`** | El hash del padre recibido en la solicitud de actualización no coincide con el registro padre.   |
| `422`  | **`crypto.signature-invalid`**   | Alguna de las firmas del registro recibido es inválida. Se activa con la primera firma inválida. |
| `422`  | **`crypto.signature-missing`**   | El registro recibido no tiene ninguna firma.                                                     |
