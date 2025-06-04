# Documentación de Microservicios - Domus

## Descripción General

Domus es una plataforma para la gestión de residenciales que utiliza una arquitectura de microservicios para proporcionar distintas funcionalidades. El sistema está construido con tecnologías como Java 21, Spring Boot, Kafka para comunicación asíncrona, y se implementa en contenedores Docker usando Docker Swarm.

## Arquitectura del Sistema

La arquitectura de Domus se compone de los siguientes componentes principales:

- **Microservicios especializados**: Cada microservicio se encarga de una responsabilidad específica
- **Kafka**: Bus de eventos para la comunicación asíncrona entre microservicios
- **Proxy API**: Gateway centralizado que dirige las peticiones a los microservicios correspondientes
- **Monitorización**: Prometheus, Grafana y Tempo para observabilidad
- **Base de datos**: PostgreSQL para persistencia de datos

### Diagrama de Arquitectura

```mermaid
flowchart TB
    Client[Cliente Web/Móvil] --> LB[Load Balancer<br>Puerto 4000]
    LB --> KP[Kafka Proxy<br>Puerto 4001]

    subgraph Microservicios
        KP --> UO[User Onboarding<br>Puerto 4002]
        KP --> NOT[Notifications<br>Puerto 4003]
        KP --> USR[Users<br>Puerto 4004]
        KP --> CHT[Chats<br>Puerto 4005]
        KP --> RO[Residential Onboarding<br>Puerto 4006]
        KP --> HOU[Houses<br>Puerto 4008]
        KP --> AC[Access Control<br>Puerto 4010]
        KP --> GU[GUARD CONTROL<br>Puerto 4011]
    end

    subgraph "Infraestructura"
        KAF[Kafka]
        PG[(PostgreSQL)]
        RED[(Redis)]
        S3[(AWS S3)]
    end

    KP <--> KAF
    UO <--> KAF
    NOT <--> KAF
    USR <--> KAF
    CHT <--> KAF
    RO <--> KAF
    HOU <--> KAF
    AC <--> KAF

    UO <--> PG
    NOT <--> PG
    USR <--> PG
    CHT <--> PG
    RO <--> PG
    HOU <--> PG
    AC <--> PG

    KP <--> RED
    NOT <--> S3
    USR <--> S3
    RO <--> S3

    subgraph Monitoreo
        PROM[Prometheus]
        GRAF[Grafana]
        TEMPO[Tempo]
    end

    LB --> PROM
    KP --> PROM
    UO --> PROM
    NOT --> PROM
    USR --> PROM
    CHT --> PROM
    RO --> PROM
    HOU --> PROM
    AC --> PROM

    PROM --> GRAF
    TEMPO --> GRAF
```

## Microservicios

### Load Balancer (Puerto: 4000)

Actúa como puerta de entrada principal al sistema, redirigiendo el tráfico a los microservicios correspondientes.

**Responsabilidades:**
- Balanceo de carga entre instancias de microservicios
- Enrutamiento de solicitudes HTTP
- Punto único de entrada para las aplicaciones cliente

### Kafka Proxy (Puerto: 4001)

Actúa como proxy API para los demás microservicios, encapsulando la lógica de comunicación vía Kafka.

**Responsabilidades:**
- Exponer endpoints REST para interactuar con todos los microservicios
- Traducir peticiones HTTP a mensajes Kafka
- Gestionar respuestas asíncronas y devolverlas al cliente

### User Onboarding (Puerto: 4002)

Gestiona el proceso de registro e incorporación de nuevos usuarios al sistema.

**Responsabilidades:**
- Registro de nuevos usuarios
- Validación de información de usuario
- Proceso de activación de cuentas

**Flujo principal:**
1. Registro inicial de usuario con datos básicos
2. Validación de correo electrónico/teléfono
3. Asignación de rol y permisos iniciales

### Notifications (Puerto: 4003)

Gestiona el envío de notificaciones a usuarios a través de diferentes canales.

**Responsabilidades:**
- Enviar notificaciones por email
- Enviar notificaciones por SMS
- Enviar notificaciones por WhatsApp
- Gestionar plantillas de notificaciones

**Canales soportados:**
- Email (mediante servicio de correo configurado)
- SMS (mediante Twilio)
- WhatsApp (mediante Twilio)

### Users (Puerto: 4004)

Gestiona la información y operaciones relacionadas con usuarios existentes.

**Responsabilidades:**
- Gestión de perfiles de usuario
- Autenticación y autorización
- Actualización de información personal
- Cambio de contraseñas y recuperación de cuentas

### Chats (Puerto: 4005)

Gestiona la comunicación entre usuarios dentro del sistema.

**Responsabilidades:**
- Mensajería instantánea entre usuarios
- Grupos de chat para residenciales
- Historial de conversaciones
- Notificaciones de mensajes nuevos

### Residential Onboarding (Puerto: 4006)

Gestiona el proceso de registro e incorporación de nuevos residenciales al sistema.

**Responsabilidades:**
- Registro de nuevos residenciales
- Configuración inicial de residenciales
- Asignación de administradores

**Flujo principal:**
1. Registro del residencial con información básica
2. Carga de logo e imágenes del residencial
3. Creación de usuario administrador para el residencial

### Houses (Puerto: 4008)

Gestiona la información y operaciones relacionadas con las casas/unidades dentro de los residenciales.

**Responsabilidades:**
- Registro de casas en residenciales
- Asignación de propietarios/inquilinos
- Gestión de información específica de cada casa
- Control de pagos mensuales y estado financiero

### Access Control (Puerto: 4010)

Gestiona el control de acceso a residenciales mediante invitaciones y permisos.

**Responsabilidades:**
- Generación de invitaciones para visitantes
- Validación de invitaciones en puntos de acceso
- Registro de entradas y salidas
- Permisos especiales para proveedores de servicios

**Flujo de invitación:**
1. Residente genera una invitación para visitante
2. Se envía notificación al visitante
3. Guardia valida invitación en entrada
4. Sistema registra entrada y salida

### Config Server (Puerto: 4400)

Gestiona la configuración centralizada para todos los microservicios.

**Responsabilidades:**
- Proveer configuración centralizada a microservicios
- Actualización dinámica de configuraciones
- Gestión de perfiles de configuración por ambiente

## Flujos de Trabajo Principales

### Registro de Usuario

1. Cliente envía petición de registro al Kafka Proxy
2. Kafka Proxy envía mensaje al User Onboarding
3. User Onboarding procesa registro y solicita envío de verificación
4. Notifications envía código de verificación
5. Usuario confirma código y completa registro
6. Users almacena información permanente del usuario

```mermaid
sequenceDiagram
    actor Usuario
    participant KP as Kafka Proxy
    participant KAF as Kafka
    participant UO as User Onboarding
    participant NOT as Notifications
    participant USR as Users
    participant DB as Base de Datos

    Usuario->>KP: Solicitud de registro
    KP->>KAF: Mensaje de registro
    KAF->>UO: Procesar registro
    UO->>DB: Crear registro temporal
    UO->>KAF: Solicitar envío de verificación
    KAF->>NOT: Enviar verificación
    NOT-->>Usuario: Envío de código por email/SMS
    Usuario->>KP: Confirmar código
    KP->>KAF: Mensaje de confirmación
    KAF->>UO: Validar código
    UO->>KAF: Crear usuario permanente
    KAF->>USR: Almacenar usuario
    USR->>DB: Guardar en BD
    USR-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Usuario: Registro completado
```

### Registro de Residencial

1. Cliente envía petición de registro de residencial al Kafka Proxy
2. Kafka Proxy envía mensaje al Residential Onboarding
3. Residential Onboarding registra información básica del residencial
4. Se registra usuario administrador del residencial
5. Se configura el residencial con opciones iniciales

```mermaid
sequenceDiagram
    actor Admin
    participant KP as Kafka Proxy
    participant KAF as Kafka
    participant RO as Residential Onboarding
    participant UO as User Onboarding
    participant S3 as AWS S3
    participant DB as Base de Datos

    Admin->>KP: Solicitud creación residencial
    KP->>KAF: Mensaje creación residencial
    KAF->>RO: Procesar solicitud
    RO->>DB: Crear registro residencial
    RO-->>KAF: Residencial creado (ID)
    KAF-->>KP: Respuesta con ID
    KP-->>Admin: ID de residencial

    Admin->>KP: Subir logo residencial
    KP->>S3: Almacenar logo
    S3-->>KP: URL del logo
    KP->>KAF: Actualizar con URL logo
    KAF->>RO: Actualizar residencial
    RO->>DB: Guardar URL logo

    Admin->>KP: Crear usuario administrador
    KP->>KAF: Mensaje creación admin
    KAF->>RO: Procesar solicitud admin
    RO->>KAF: Solicitar creación usuario
    KAF->>UO: Crear usuario admin
    UO->>DB: Guardar usuario con rol admin
    UO-->>KAF: Usuario creado
    KAF-->>RO: Confirmar creación
    RO->>DB: Asociar admin a residencial
    RO-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Admin: Configuración completada
```

### Registro de Casa

1. Administrador de residencial registra nueva casa
2. Se asigna identificador único a la casa
3. Se pueden registrar propietarios/inquilinos
4. Se configura información de pagos mensuales

```mermaid
sequenceDiagram
    actor Admin
    participant KP as Kafka Proxy
    participant KAF as Kafka
    participant HOU as Houses
    participant USR as Users
    participant DB as Base de Datos

    Admin->>KP: Registrar nueva casa
    KP->>KAF: Mensaje creación casa
    KAF->>HOU: Procesar solicitud
    HOU->>DB: Crear registro casa
    HOU-->>KAF: Casa creada
    KAF-->>KP: Respuesta con ID
    KP-->>Admin: ID de casa

    Admin->>KP: Asignar propietario/inquilino
    KP->>KAF: Mensaje asignación
    KAF->>HOU: Procesar asignación
    HOU->>KAF: Verificar usuario
    KAF->>USR: Consultar usuario
    USR-->>KAF: Datos usuario
    KAF-->>HOU: Usuario verificado
    HOU->>DB: Asociar usuario a casa
    HOU-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Admin: Asignación completada

    Admin->>KP: Configurar pagos mensuales
    KP->>KAF: Mensaje configuración pagos
    KAF->>HOU: Procesar configuración
    HOU->>DB: Actualizar información pagos
    HOU-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Admin: Configuración completada
```

### Creación de Invitación

1. Residente solicita creación de invitación para visitante
2. Access Control genera código único de invitación
3. Notifications envía invitación al visitante
4. Guardia valida invitación en entrada
5. Sistema registra entrada y salida

```mermaid
sequenceDiagram
    actor Residente
    actor Guardia
    actor Visitante
    participant KP as Kafka Proxy
    participant KAF as Kafka
    participant AC as Access Control
    participant NOT as Notifications
    participant DB as Base de Datos

    Residente->>KP: Solicitar invitación
    KP->>KAF: Mensaje creación invitación
    KAF->>AC: Procesar solicitud
    AC->>DB: Crear registro invitación
    AC->>KAF: Solicitar envío invitación
    KAF->>NOT: Enviar invitación
    NOT-->>Visitante: Envío de código por email/SMS

    Visitante->>Guardia: Presentar código
    Guardia->>KP: Validar invitación
    KP->>KAF: Mensaje validación
    KAF->>AC: Verificar código
    AC->>DB: Consultar invitación
    AC->>DB: Registrar entrada
    AC-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Guardia: Invitación válida

    Note over Guardia,KP: Al salir
    Guardia->>KP: Registrar salida
    KP->>KAF: Mensaje salida
    KAF->>AC: Procesar salida
    AC->>DB: Registrar salida
    AC-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Guardia: Salida registrada
```

### Comunicación vía Chat

1. Usuario inicia conversación con otro usuario o grupo
2. Chats gestiona el envío y recepción de mensajes
3. Notifications envía alertas de nuevos mensajes
4. Se mantiene historial de conversaciones

```mermaid
sequenceDiagram
    actor Usuario1
    actor Usuario2
    participant KP as Kafka Proxy
    participant KAF as Kafka
    participant CHT as Chats
    participant NOT as Notifications
    participant DB as Base de Datos

    Usuario1->>KP: Iniciar conversación
    KP->>KAF: Mensaje nueva conversación
    KAF->>CHT: Crear conversación
    CHT->>DB: Guardar conversación
    CHT-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Usuario1: Conversación iniciada

    Usuario1->>KP: Enviar mensaje
    KP->>KAF: Mensaje nuevo chat
    KAF->>CHT: Procesar mensaje
    CHT->>DB: Guardar mensaje
    CHT->>KAF: Solicitar notificación
    KAF->>NOT: Enviar notificación
    NOT-->>Usuario2: Notificación de mensaje
    CHT-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Usuario1: Mensaje enviado

    Usuario2->>KP: Obtener mensajes
    KP->>KAF: Solicitud mensajes
    KAF->>CHT: Consultar mensajes
    CHT->>DB: Leer historial
    CHT-->>KAF: Lista de mensajes
    KAF-->>KP: Mensajes recuperados
    KP-->>Usuario2: Mostrar mensajes

    Usuario2->>KP: Marcar como leído
    KP->>KAF: Actualizar estado
    KAF->>CHT: Procesar actualización
    CHT->>DB: Actualizar estado
    CHT-->>KAF: Confirmación
    KAF-->>KP: Respuesta exitosa
    KP-->>Usuario2: Estado actualizado
    KAF->>NOT: Actualizar estado remoto
    NOT-->>Usuario1: Notificación leído
```

## Tecnologías Utilizadas

- **Lenguaje**: Java 21
- **Framework**: Spring Boot, Spring MVC, Spring Data JPA
- **Mensajería**: Apache Kafka
- **Base de Datos**: PostgreSQL
- **Contenedores**: Docker, Docker Swarm
- **Monitorización**: Prometheus, Grafana, Tempo, Loki
- **CI/CD**: Jenkins Pipeline
- **Almacenamiento**: AWS S3
- **Notificaciones**: Twilio, SMTP

## Monitorización y Observabilidad

Todos los microservicios exponen métricas a través de Prometheus en el endpoint `/actuator/prometheus`. Estas métricas son recopiladas y visualizadas en dashboards de Grafana para monitoreo en tiempo real.

La trazabilidad distribuida se implementa con OpenTelemetry y Tempo, permitiendo seguir el flujo de una solicitud a través de todos los microservicios involucrados.

## Seguridad

La seguridad se implementa a través de:

- Autenticación basada en tokens JWT
- Comunicación cifrada entre microservicios
- Validación de entradas en todos los endpoints
- Gestión centralizada de secretos
- Control de acceso basado en roles
