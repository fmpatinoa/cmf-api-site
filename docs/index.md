---
title: Perfil de API CMF v1.0
---

###**Descripción general**
El perfil de API de lectura/escritura de datos proporciona una descripción de los elementos que son comunes a todas las API de lectura/escritura de datos.

Este perfil debe utilizarse junto con perfiles funcionales compatibles (como Cuentas y Transacciones o Pagos) y recursos compatibles.

###**Estructura del documento**
Este documento consta de las siguientes partes:

* **Descripción general** : proporciona una descripción general del perfil y de las decisiones y principios clave.
* **Conceptos básicos** : la sección comienza con una introducción sobre cómo se utilizan las API.
* **Seguridad y control de acceso** : especifica los medios para que los TPP y las PSU se autentiquen y brinden su consentimiento.
* **Modelo de datos** : describe el modelo de datos para las cargas útiles de la API.
* **Ejemplos de uso** : Ejemplos de uso para paginación y flujos de error.

###**Principios de diseño**

####**API RESTful**
La API se adhiere a los conceptos de API RESTful siempre que sea posible y sensato hacerlo.

Sin embargo, la prioridad es tener una API que sea fácil de entender y de usar. En los casos en los que seguir los principios RESTful sería complicado y complejo, no se han seguido los principios.

Referencias:

* El lenguaje de descripción de datos de nivel más alto utilizado es el esquema JSON: http://json-schema.org/(abre nueva ventana)
* Las mejores prácticas también se han tomado del lenguaje de descripción de datos para API; API JSON: http://jsonapi.org/(abre nueva ventana)
* El lenguaje de descripción de interfaz utilizado es la especificación Open API v3 (anteriormente conocida como Swagger): https://www.openapis.org (abre nueva ventana)https://github.com/OAI/OpenAPI-Specification(abre nueva ventana)

**Normas**

Los principios OBL para el desarrollo de estándares API:

* OBL adoptará los estándares existentes cuando sea relevante o apropiado para minimizar la necesidad de reinventar la rueda.
* Las normas que se están revisando actualmente incluyen ISO20022 y FAPI.
* OBL favorecerá la experiencia del desarrollador/usuario por encima de la adopción de estándares existentes, con el fin de crear un estándar más preparado para el futuro.
* OBL trabajará con otros organismos relevantes para alinearse, contribuir y/o adoptar otros trabajos de estándares, especialmente relacionados con la creación de estándares en torno a API y cargas útiles JSON