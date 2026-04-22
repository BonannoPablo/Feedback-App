# Plan: Feedback App

## Endpoints API (servicios al resto del sistema)

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| POST | `/api/feedback` | Crear calificación |
| GET | `/api/feedback/:userId` | Obtener historial de calificaciones |
| GET | `/api/rating/:userId` | Obtener rating promedio |
| POST | `/api/reports` | Crear reporte |
| GET | `/api/reports/:userId` | Obtener reportes contra usuario |
| GET | `/api/user/:id` | Obtener datos públicos de usuario (nombre, rating, foto) |

## Frontend (páginas propias)

| Página | Descripción |
|--------|-------------|
| `/historial` | Ver todas las calificaciones recibidas y emitidas |
| `/calificar/[serviceId]` | Formulario para calificar post-servicio |
| `/perfil/[userId]` | Ver perfil público y rating de usuario |
| `/reportar/[userId]` | Formulario de denuncia |
| `/mis-reportes` | Ver mis reportes enviados |

## Integración con otras apps

- **Clerk**: Autenticación unificada
- **Users/Towers API**: Obtener lista de servicios completados para calificar
- **Payments API**: (opcional) verificar pago si se requiere antes de calificar

## Flujo de calificaciones

1. El Tower notifica al User que el servicio finalized.
2. Tower y User pueden calificar el servicio a partir de su página de historial de "viajes"
3. Las calificaciones pueden realizarse en cualquier momento una vez completado el servicio.
4. Las calificaciones User→Tower y Tower→User son independientes.

## Flujo de reportes

1. Cualquier usuario puede denunciar a otro desde su historial de viajes (es necesario que haya habido un servicio entre ellos).
2. Sistema de suspensión: automático y manual (detalles aún por definir).

## Modelo de Datos

### Feedback

| Campo | Tipo | Descripción |
|-------|------|-------------|
| id | UUID | PK |
| serviceId | UUID | ID del servicio (referencia externa) |
| raterClerkId | UUID | Clerk ID de quien califica |
| ratedClerkId | UUID | Clerk ID del calificado |
| rating | INTEGER | 1-5 estrellas |
| comment | TEXT | Comentario opcional |
| type | ENUM | 'user_to_tower', 'tower_to_user' |
| createdAt | TIMESTAMP | Fecha de creación |

### Report

| Campo | Tipo | Descripción |
|-------|------|-------------|
| id | UUID | PK |
| reporterClerkId | UUID | Clerk ID de quien reporta |
| reportedClerkId | UUID | Clerk ID del reportado |
| serviceId | UUID | ID del servicio relacionado |
| reason | ENUM | 'conducta', 'vehiculo', 'otro' |
| description | TEXT | Descripción de la denuncia |
| status | ENUM | 'pendiente', 'revisado', 'resuelto' |
| createdAt | TIMESTAMP | Fecha de creación |

### UserRating (cache de rating)

| Campo | Tipo | Descripción |
|-------|------|-------------|
| clerkId | UUID | PK |
| avgRating | DECIMAL | Rating promedio |
| totalRatings | INTEGER | Cantidad de calificaciones |
| updatedAt | TIMESTAMP | Última actualización |

### Relaciones

- Un `Report` puede aplicarse a un `Feedback` (mismo serviceId)
- `UserRating` se actualiza con triggers cuando se inserta un `Feedback`

## Verificación de acceso a /calificar/[serviceId]

### Problemas

| Problema | Solución |
|----------|----------|
| Identificar si es tower o user | Clerk tiene el `userId` en el token JWT |
| Validar participación | Llamar a Users/Towers API para verificar que el servicio fue completado con ese usuario |
| Evitar acceso no autorizado | Si no participa → redirigir o mostrar error 403 |

### Flujo propuesto

```
1. Usuario accede a /calificar/[serviceId]
2. Clerk autentica → obtengo clerkId del token
3. Llamar Users/Towers API → GET /servicios/{serviceId}
4. Verificar que clerkId está en (userId o towerId) del servicio
5. Si no participa → error 403
6. Si participa → mostrar formulario de calificación
```