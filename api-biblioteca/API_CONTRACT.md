# Contrato de API — Gestión de Biblioteca (Libros y Reservas)

## 1. URL Base y Puerto

- **URL base local**: `http://localhost:3000`
- **Puerto por defecto**: `3000` (configurable mediante `PORT` en `.env`)
- **Formato de datos**: `application/json` (enviar cabecera `Content-Type: application/json` en peticiones con body)

---

## 2. Lista de Endpoints

### Módulo de Libros (`/api/books`)

| Método | Ruta | Parámetros | Descripción |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/books` | — | Lista todos los libros ordenados por `id` ascendente. |
| `GET` | `/api/books/:id` | `:id` (entero positivo, obligatorio en ruta) | Obtiene un libro por su ID. |
| `POST` | `/api/books` | — | Crea un nuevo libro. |
| `PUT` | `/api/books/:id` | `:id` (entero positivo, obligatorio en ruta) | Actualiza uno o más campos de un libro existente. |
| `DELETE` | `/api/books/:id` | `:id` (entero positivo, obligatorio en ruta) | Elimina un libro por su ID. |

### Módulo de Reservas (`/api/reservations`)

| Método | Ruta | Parámetros | Descripción |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/reservations` | — | Lista todas las reservas (incluye `user` y `book`) ordenadas por `id` descendente. |
| `GET` | `/api/reservations/:id` | `:id` (entero positivo, obligatorio en ruta) | Obtiene una reserva por su ID (incluye `user` y `book`). |
| `POST` | `/api/reservations` | — | Crea una reserva para un usuario y un libro disponible. |
| `PATCH` | `/api/reservations/:id/return` | `:id` (entero positivo, obligatorio en ruta) | Registra la devolución del libro asociado a la reserva. |
| `PUT` | `/api/reservations/:id/return` | `:id` (entero positivo, obligatorio en ruta) | Alias de `PATCH /api/reservations/:id/return`. |

---

## 3. Ejemplos de Request Body (`POST`, `PUT`, `PATCH`)

### `POST /api/books`

`disponibilidad` es opcional (por defecto `true`).

```json
{
  "titulo": "Cien años de soledad",
  "autor": "Gabriel García Márquez",
  "isbn": "978-0307474728",
  "año_publicacion": 1967,
  "disponibilidad": true
}
```

### `PUT /api/books/:id`

Admite enviar todos los campos o solo los que se desean modificar (al menos un campo obligatorio).

```json
{
  "titulo": "Cien años de soledad (Edición Conmemorativa)",
  "año_publicacion": 2007,
  "disponibilidad": false
}
```

### `POST /api/reservations`

Acepta `user_id` / `userId`, `book_id` / `bookId` y `fecha_devolucion_esperada` en formato ISO 8601.

```json
{
  "user_id": 1,
  "book_id": 1,
  "fecha_devolucion_esperada": "2026-11-25T23:59:59.000Z"
}
```

### `PATCH /api/reservations/:id/return` (o `PUT /api/reservations/:id/return`)

No requiere body.

---

## 4. Ejemplos de Respuestas JSON

### Respuestas de Éxito — Libros

**`GET /api/books` — `200 OK`**
```json
[
  {
    "id": 1,
    "titulo": "Cien años de soledad",
    "autor": "Gabriel García Márquez",
    "isbn": "978-0307474728",
    "año_publicacion": 1967,
    "disponibilidad": true
  }
]
```

**`GET /api/books/:id` — `200 OK`** / **`PUT /api/books/:id` — `200 OK`**
```json
{
  "id": 1,
  "titulo": "Cien años de soledad",
  "autor": "Gabriel García Márquez",
  "isbn": "978-0307474728",
  "año_publicacion": 1967,
  "disponibilidad": true
}
```

**`POST /api/books` — `201 Created`**
```json
{
  "id": 1,
  "titulo": "Cien años de soledad",
  "autor": "Gabriel García Márquez",
  "isbn": "978-0307474728",
  "año_publicacion": 1967,
  "disponibilidad": true
}
```

**`DELETE /api/books/:id` — `200 OK`**
```json
{
  "message": "Libro eliminado correctamente."
}
```

### Respuestas de Éxito — Reservas

**`GET /api/reservations` — `200 OK`**
```json
[
  {
    "id": 1,
    "fecha_reserva": "2026-11-18T15:00:00.000Z",
    "fecha_devolucion_esperada": "2026-11-25T23:59:59.000Z",
    "fecha_devolucion_real": null,
    "estado": "ACTIVA",
    "user": {
      "id": 1,
      "nombre": "Ana García",
      "email": "ana@ejemplo.com"
    },
    "book": {
      "id": 1,
      "titulo": "Cien años de soledad",
      "autor": "Gabriel García Márquez",
      "isbn": "978-0307474728",
      "año_publicacion": 1967,
      "disponibilidad": false
    }
  }
]
```

**`GET /api/reservations/:id` — `200 OK`** / **`POST /api/reservations` — `201 Created`**
```json
{
  "id": 1,
  "fecha_reserva": "2026-11-18T15:00:00.000Z",
  "fecha_devolucion_esperada": "2026-11-25T23:59:59.000Z",
  "fecha_devolucion_real": null,
  "estado": "ACTIVA",
  "user": {
    "id": 1,
    "nombre": "Ana García",
    "email": "ana@ejemplo.com"
  },
  "book": {
    "id": 1,
    "titulo": "Cien años de soledad",
    "autor": "Gabriel García Márquez",
    "isbn": "978-0307474728",
    "año_publicacion": 1967,
    "disponibilidad": false
  }
}
```

**`PATCH /api/reservations/:id/return` (o `PUT /api/reservations/:id/return`) — `200 OK`**
```json
{
  "id": 1,
  "fecha_reserva": "2026-11-18T15:00:00.000Z",
  "fecha_devolucion_esperada": "2026-11-25T23:59:59.000Z",
  "fecha_devolucion_real": "2026-11-18T15:30:00.000Z",
  "estado": "DEVUELTA",
  "user": {
    "id": 1,
    "nombre": "Ana García",
    "email": "ana@ejemplo.com"
  },
  "book": {
    "id": 1,
    "titulo": "Cien años de soledad",
    "autor": "Gabriel García Márquez",
    "isbn": "978-0307474728",
    "año_publicacion": 1967,
    "disponibilidad": true
  }
}
```

### Respuestas de Error Comunes

**`400 Bad Request` — Validación de campos en Libros (`POST` / `PUT`)**
```json
{
  "message": "Datos inválidos.",
  "errors": [
    "El campo \"titulo\" es obligatorio y debe ser texto no vacío.",
    "El campo \"año_publicacion\" es obligatorio y debe ser un número entero positivo."
  ]
}
```

**`400 Bad Request` — Body vacío en `PUT /api/books/:id`**
```json
{
  "message": "Debes proporcionar al menos un campo para actualizar."
}
```

**`400 Bad Request` — Parámetro `:id` inválido**
```json
{
  "message": "El ID proporcionado no es válido."
}
```
*(En `/api/reservations/:id` y `/api/reservations/:id/return` responde:)*
```json
{
  "message": "El ID de la reserva no es válido."
}
```

**`400 Bad Request` — Body inválido en `POST /api/reservations`**
```json
{
  "message": "Los campos \"user_id\", \"book_id\" (enteros positivos) y \"fecha_devolucion_esperada\" (fecha válida) son obligatorios."
}
```

**`400 Bad Request` — Intento de devolver una reserva ya devuelta (`PATCH`/`PUT /api/reservations/:id/return`)**
```json
{
  "message": "Esta reserva ya fue devuelta anteriormente."
}
```

**`404 Not Found` — Recurso o ruta inexistente**
```json
{
  "message": "Libro no encontrado."
}
```
*(Otros mensajes `404` posibles según el caso: `"Usuario no encontrado."`, `"Reserva no encontrada."`, `"Ruta no encontrada."`)*

**`409 Conflict` — ISBN duplicado en Libros (`POST` / `PUT`)**
```json
{
  "message": "Ya existe un libro registrado con ese ISBN."
}
```

**`409 Conflict` — Libro no disponible al reservar (`POST /api/reservations`)**
```json
{
  "message": "El libro no está disponible para reserva."
}
```

**`500 Internal Server Error` — Error no controlado**
```json
{
  "message": "Error interno del servidor."
}
```

---

## 5. Reglas de Negocio Visibles para el Cliente

1. **Ciclo automático de disponibilidad del libro**:
   - Todo libro creado con `POST /api/books` inicia con `disponibilidad: true` salvo que el cliente envíe explícitamente `disponibilidad: false`.
   - Al crearse una reserva exitosa (`POST /api/reservations`), el backend cambia automáticamente el libro a `disponibilidad: false` dentro de la misma transacción.
   - Al registrarse la devolución (`PATCH` o `PUT /api/reservations/:id/return`), el backend actualiza `fecha_devolucion_real` con la fecha/hora actual del servidor, cambia `estado` a `"DEVUELTA"` y restaura el libro a `disponibilidad: true`.
2. **Restricción para nuevas reservas**:
   - Solo se puede reservar un libro cuando `disponibilidad === true`. Si ya está prestado (`disponibilidad === false`), la API rechaza la operación con `409 Conflict`.
   - Tanto el usuario (`user_id`) como el libro (`book_id`) deben existir previamente; de lo contrario retorna `404 Not Found`.
3. **Estados de una reserva**:
   - Valores posibles del campo `estado`: `"ACTIVA"`, `"DEVUELTA"`, `"VENCIDA"`.
   - Toda reserva recién creada inicia con `estado: "ACTIVA"` y `fecha_devolucion_real: null`.
   - Una reserva con `estado: "DEVUELTA"` no puede volver a devolverse (retorna `400 Bad Request`). Las reservas `"ACTIVA"` o `"VENCIDA"` sí pueden devolverse.
4. **Unicidad de ISBN y validaciones de entrada**:
   - El campo `isbn` es único en todo el catálogo de libros; intentar crear o actualizar un libro con un `isbn` existente retorna `409 Conflict`.
   - Los identificadores `:id`, `user_id` y `book_id` deben ser números enteros positivos (`> 0`).
   - `año_publicacion` debe ser un entero positivo (`> 0`), `disponibilidad` debe ser booleano (`true`/`false`), y `titulo`, `autor` e `isbn` deben ser cadenas de texto no vacías.
