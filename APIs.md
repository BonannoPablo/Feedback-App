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