Este es el proyecto de desarrollo de la app de feedback para la app TowIt

El proyecto debe ser implementado con Next.js

## Contexto

Necesitamos desarrollar una aplicación web que permita conectar a personas con capacidad de remolcar un vehículo con personas que tengan un vehículo averiado y requiera que lo remolquen, similar a como Uber conecta gente que tiene la capacidad de transporte y gente que necesita transportarse. 

La app tiene que estar compuesta de cuatro aplicaciones full stack independientes:
 - "Driver app (Tower app)" 
 - "Rider app" (User app)
 - "Payments app"
 - "Feedback app"

## Requisitos de cada app
 - API propia. Cada app debe exponer al menos un endpoint REST
 - Base de datos PostgreSQL
 - Autenticación mediante Clerk
 - Consumo de al menos una API externa (Incluye las APIs proporcionadas por las demas apps del sistema)

## Glosario

 - Tower: equivalente al driver de Uber. Es quien presta los servicios de remolque.
 - User: equivalente al rider de Uber. Es quien utiliza el servicio de remolque.