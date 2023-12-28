# Blog de Ghost en Kubernetes

Este repositorio contiene manifiestos de Kubernetes para implementar un blog de Ghost junto con una base de datos MySQL. La configuración está organizada utilizando Kustomize para una mejor gestión y personalización.

## Contenido

- **base/namespace.yaml**: Define el espacio de nombres de Kubernetes para la implementación de Ghost.
- **base/secrets.yaml**: Contiene secretos genéricos utilizados tanto por Ghost como por MySQL.
- **app/ghost/deployment.yaml**: Describe la implementación de la aplicación Ghost.
- **app/ghost/configmap.yaml**: ConfigMap para la configuración de la aplicación Ghost.
- **app/ghost/secrets.yaml**: Secretos específicos de la aplicación Ghost.
- **app/ghost/service.yaml**: Configuración del servicio para la aplicación Ghost.
- **app/ghost/pvc.yaml**: Reclamación de volumen persistente para el almacenamiento de Ghost.
- **app/ghost/ingress.yaml**: Configuración de Ingress para acceder al blog de Ghost.
- **app/mysql/deployment.yaml**: Describe la implementación de la base de datos MySQL.
- **app/mysql/service.yaml**: Configuración del servicio para la base de datos MySQL.
- **app/mysql/pvc.yaml**: Reclamación de volumen persistente para el almacenamiento de MySQL.

## Configuración de la Implementación de Ghost

### Implementación

- **Replicas**: 1
- **Estrategia**: Recrear
- **Imagen del Contenedor**: ghost:alpine
- **Recursos**:
  - Solicitudes: CPU=250m, Memoria=180Mi
  - Límites: CPU=350m, Memoria=300Mi
- **Puertos**: ContainerPort=2368

### Sondas

- **Sonda de Disponibilidad**: Verifica si Ghost está listo para aceptar tráfico.
- **Sonda de Supervivencia**: Verifica si Ghost está vivo y saludable.

### Variables de Entorno

- Ghost depende de variables de entorno para la configuración, incluyendo detalles de conexión a la base de datos y configuración de Mailgun.

## Configuración de Ingress

- **Hosts**: questverse.blog, www.questverse.blog
- **TLS**: Utiliza el secreto questverse-com-tls para HTTPS.
- **Anotaciones**:
  - Redirección de from-to-www.
  - Varios ajustes de NGINX para proxy, almacenamiento en búfer, caché y brotli.

## Configuración de Ghost

### ConfigMap

- ConfigMap llamado `ghost-configmap` con pares clave-valor para la configuración de Ghost, incluyendo URL, conexión a la base de datos y configuración de correo.

### Almacenamiento Persistente

- Ghost utiliza una Reclamación de Volumen Persistente (PVC) llamada `ghost` con una solicitud de almacenamiento de 15Gi y la clase de almacenamiento `do-block-storage`.

### Secretos

- Secretos llamados `ghost-secrets` para datos sensibles, incluyendo credenciales de Mailgun.

### Servicio

- Servicio de Kubernetes llamado `ghost` con asignación de puertos (80:ghost).

## Configuración de la Implementación de MySQL

### Implementación

- **Replicas**: 1
- **Estrategia**: Recrear
- **Imagen del Contenedor**: mysql:8.0.33
- **Recursos**:
  - Solicitudes: CPU=200m, Memoria=450Mi
  - Límites: CPU=400m, Memoria=550Mi
- **Puertos**: ContainerPort=3306

### Sondas

- **Sonda de Disponibilidad**: Verifica si MySQL está listo para aceptar tráfico.
- **Sonda de Supervivencia**: Verifica si MySQL está vivo y saludable.

### Variables de Entorno

- La configuración de MySQL incluye la contraseña de root, el nombre de la base de datos y depende de secretos.

### Almacenamiento Persistente

- MySQL utiliza una Reclamación de Volumen Persistente (PVC) llamada `mysql` con una solicitud de almacenamiento de 5Gi y la clase de almacenamiento `do-block-storage`.

### Servicio

- Servicio de Kubernetes llamado `mysql` con clusterIP configurado como None para un servicio sin cabeza.