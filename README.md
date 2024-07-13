# PrivaNota
## Ejemplos de clientes
- [Página web](https://antikorps13.neocities.org/privanota/): simple prototipo para comprobar el funcionamiento de la API Rest.

## BackEnd
El backend se fundamenta en [PocketBase](https://pocketbase.io/) y está alojado en [PocketHost](https://pockethost.io/) (mantenido y proporcionado por [benallfree](https://github.com/benallfree))


## Sobre la encriptacion/desencriptación
El único contenido que no debe encriptarse es el username. Por limitaciones actuales en el backend, este campo debe cumplir con la siguiente expresión regular: 
```code
^[\w][\w\.]*$
```
Aunque cada cliente puede gestionar de manera libre esta encriptación y desencriptación, para asegurar la máxima compatibilidad entre ellos se recomienda cumplir las siguientes indicaciones.

1. **AES (Advanced Encryption Standard)**: Es un algoritmo de cifrado simétrico ampliamente utilizado. AES 256 utiliza una clave de 256 bits (32 bytes).
2. **Clave (Key)**: Es una cadena de 256 bits (32 bytes) utilizada tanto para cifrar como para descifrar el mensaje.
3. **IV (Initialization Vector)**: Es una cadena de 128 bits (16 bytes) que se usa para agregar aleatoriedad al proceso de cifrado. Para obtener resultados consistentes, se propone el uso de un IV fijo: "1234567890123456" (sin comillas, donde el contenido es una cadena UTF-8 de 16 caracteres).

## Límites
El campo **title** o nombre del archivo no puede superar los 250 caracteres.

El campo **content** o contenido del archivo no puede superar los 1200 caracteres.



# API REST

Base URL
---

`https://privanota.pockethost.io`


## Users
### Registro de usuario

Registra un nuevo usuario en el sistema.

*   **URL:** `/api/collections/users/records`
*   **Método:** `POST`
*   **Headers:**

*   `Content-Type: application/json`

*   **Body:**
```json
    {
      "username": "nombre_de_usuario",
      "password": "contraseña_cifrada",
      "passwordConfirm": "contraseña_cifrada"
    }
```
**Respuesta:**

**200**
```json
{
  "id": "RECORD_ID",
  "collectionId": "_pb_users_auth_",
  "collectionName": "users",
  "username": "username123",
  "verified": false,
  "emailVisibility": true,
  "email": "test@example.com",
  "created": "2022-01-01 01:00:00.123Z",
  "updated": "2022-01-01 23:59:59.456Z",
  "name": "test",
  "avatar": "filename.jpg"
}
```

**400**
```json
{
  "code": 400,
  "message": "Failed to create record.",
  "data": {
    "name": {
      "code": "validation_required",
      "message": "Missing required value."
    }
  }
}
```
**403**
```json
{
  "code": 403,
  "message": "You are not allowed to perform this request.",
  "data": {}
}
```


### Login de usuario
Loguea a un usuario existente.

*   **URL:** `/api/collections/users/auth-with-password`
*   **Método:** `POST`
*   **Headers:**

*   `Content-Type: application/json`

*   **Body:**
```json

    {
      "identity": "nombre_de_usuario",
      "password": "contraseña_cifrada"
    }
```
**Respuesta:**

**200**
```json
{
  "token": "JWT_TOKEN",
  "record": {
    "id": "RECORD_ID",
    "collectionId": "_pb_users_auth_",
    "collectionName": "users",
    "username": "username123",
    "verified": false,
    "emailVisibility": true,
    "email": "test@example.com",
    "created": "2022-01-01 01:00:00.123Z",
    "updated": "2022-01-01 23:59:59.456Z",
    "name": "test",
    "avatar": "filename.jpg"
  }
}

```
***400**
```json
{
  "code": 400,
  "message": "Failed to authenticate.",
  "data": {
    "identity": {
      "code": "validation_required",
      "message": "Missing required value."
    }
  }
}
```

### Eliminación de usuario
Elimina a un usuario existente.

*   **URL:** `/api/collections/users/records/{userId}`
*   **Método:** `DELETE`
*   **Headers:**

*   `Authorization: Bearer {token}`
*   `Content-Type: application/json`

**Respuesta**

**204**
```
null
```
**400**
```json
{
  "code": 400,
  "message": "Failed to delete record. Make sure that the record is not part of a required relation reference.",
  "data": {}
}
```
**404**
```json
{
  "code": 404,
  "message": "The requested resource wasn't found.",
  "data": {}
}

```

## Files
### Cargar Archivos

Carga todos los archivos cifrados de un usuario.

*   **URL:** `/api/collections/files/records?page={pagina}`
*   **Método:** `GET`
*   **Headers:**

*   `Authorization: Bearer {token}`
*   `Content-Type: application/json`

**Respuesta:**
**200**
```json
{
  "page": 1,
  "perPage": 30,
  "totalPages": 1,
  "totalItems": 2,
  "items": [
    {
      "id": "RECORD_ID",
      "collectionId": "f8hgy12xkmtmecq",
      "collectionName": "files",
      "created": "2022-01-01 01:00:00.123Z",
      "updated": "2022-01-01 23:59:59.456Z",
      "title": "test",
      "content": "test",
      "user_id": "test"
    },
    {
      "id": "RECORD_ID",
      "collectionId": "f8hgy12xkmtmecq",
      "collectionName": "files",
      "created": "2022-01-01 01:00:00.123Z",
      "updated": "2022-01-01 23:59:59.456Z",
      "title": "test",
      "content": "test",
      "user_id": "test"
    }
  ]
}
```
**400**
```json
{
  "code": 400,
  "message": "Something went wrong while processing your request. Invalid filter.",
  "data": {}
}
```

### Crear archivo

Crea un nuevo archivo cifrado.

*   **URL:** `/api/collections/files/records/`
*   **Método:** `POST`
*   **Headers:**

*   `Authorization: Bearer {token}`
*   `Content-Type: application/json`

*   **Body:**
```json

    {
      "title": "titulo_cifrado",
      "content": "contenido_cifrado",
      "user_id": "id_del_usuario"
    }
```
**Respuesta:**

**200**
```json
{
  "id": "RECORD_ID",
  "collectionId": "f8hgy12xkmtmecq",
  "collectionName": "files",
  "created": "2022-01-01 01:00:00.123Z",
  "updated": "2022-01-01 23:59:59.456Z",
  "title": "test",
  "content": "test",
  "user_id": "test"
}
```
**400**
```json
{
  "code": 400,
  "message": "Failed to create record.",
  "data": {
    "title": {
      "code": "validation_required",
      "message": "Missing required value."
    }
  }
}
```
**403**
```json
{
  "code": 403,
  "message": "You are not allowed to perform this request.",
  "data": {}
}
```

### Actualizar archivo

Actualiza un archivo cifrado existente.

*   **URL:** `/api/collections/files/records/{fileId}`
*   **Método:** `PATCH`
*   **Headers:**

*   `Authorization: Bearer {token}`
*   `Content-Type: application/json`

*   **Body:**
```json

    {
      "title": "titulo_cifrado",
      "content": "contenido_cifrado",
      "user_id": "id_del_usuario"
    }
```
**Respuesta:**
**200**
```json
{
  "id": "RECORD_ID",
  "collectionId": "f8hgy12xkmtmecq",
  "collectionName": "files",
  "created": "2022-01-01 01:00:00.123Z",
  "updated": "2022-01-01 23:59:59.456Z",
  "title": "test",
  "content": "test",
  "user_id": "test"
}
```
**400**
```json
{
  "code": 400,
  "message": "Failed to update record.",
  "data": {
    "title": {
      "code": "validation_required",
      "message": "Missing required value."
    }
  }
}
```
**403**
```json
{
  "code": 403,
  "message": "You are not allowed to perform this request.",
  "data": {}
}
```
**404**
```json
{
  "code": 404,
  "message": "The requested resource wasn't found.",
  "data": {}
}
```


### Eliminar archivo

Elimina un archivo cifrado existente.

*   **URL:** `/api/collections/files/records/{fileId}`
*   **Método:** `DELETE`
*   **Headers:**

*   `Authorization: Bearer {token}`
*   `Content-Type: application/json`

**Respuesta:**
**204**
```
null
```
**400**
```json
{
  "code": 400,
  "message": "Failed to delete record. Make sure that the record is not part of a required relation reference.",
  "data": {}
}

```
**404**
```json
{
  "code": 404,
  "message": "The requested resource wasn't found.",
  "data": {}
}

```
