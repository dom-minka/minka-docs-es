---
icon: lock
---

# Errores de seguridad

Estos errores están relacionados con la autenticación y las reglas de acceso de seguridad. Se producen cuando las credenciales de seguridad contenidas en el JWT de la solicitud son inválidas o cuando se prohíbe realizar alguna acción en el libro mayor con las credenciales presentadas.

Estos errores ocurren durante el procesamiento de solicitudes de API, por lo que tienen códigos de estado HTTP de respuesta asociados.

| Status | Detail                  | Reason                                                                                                                                                                                         |
| ------ | ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `401`  | **`auth.unauthorized`** | Las credenciales de seguridad faltan o las credenciales presentadas son inválidas o han expirado. Las credenciales son inválidas cuando la firma JWT es inválida o el payload JWT es inválido. |
| `403`  | **`auth.forbidden`**    | No se permite realizar una acción en el libro mayor o en el registro con las credenciales de seguridad actuales según las reglas de acceso.                                                    |

