## 🚀 Infraestructura Servidor CREADO POR EL MACHO LOMO PLATEADO IRAGUI.
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/) 
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/) 
[![Debian](https://img.shields.io/badge/Debian-%23D70A53.svg?style=for-the-badge&logo=debian&logoColor=white)](https://www.debian.org/) 
[![n8n](https://img.shields.io/badge/n8n-FF6C37?style=for-the-badge&logo=n8n&logoColor=white)](https://n8n.io/)


Este repositorio contiene la configuración base para desplegar un entorno de automatización y servicios web en un servidor Debian/Ubuntu utilizando Docker.

## 🛠️ Servicios incluidos
* **Caddy:** Servidor web con SSL automático (HTTPS).
* **n8n:** Herramienta de automatización de flujos de trabajo.
* **MySQL 8.0:** Motor de base de datos para la aplicación PHP y otros servicios.
* **PHP:** Contenedor configurado para procesar lógica web.
* **WireGuard:** VPN para acceso seguro al servidor.
* **phpMyAdmin:** Interfaz web para gestionar la base de datos (Puerto 8080).

## 📋 Requisitos previos
1.  Tener **Docker** y **Docker Compose** instalados.
2.  Un dominio apuntando a la IP del servidor (ej: `tuhermana.net`).
3.  Abrir los puertos necesarios en el Security Group de AWS (80, 443, 51820 UDP).

## 🚀 Instalación rápida

1.  **Clonar el repositorio:**
    ```bash
    git clone [https://github.com/Giragui/Infra-Servidor-AWS.git](https://github.com/Giragui/Infra-Servidor-AWS.git)
    cd Infra-Servidor-AWS
    ```

2.  **Configurar variables de entorno:**
    Copia el archivo de ejemplo y edítalo con tus claves reales:
    ```bash
    cp env.example .env
    nano .env
    ```

3.  **Configurar el Caddyfile:**
    Asegúrate de reemplazar `"TU DOMINIO"` por tu dominio real en el archivo `Caddyfile`.

4.  **Levantar los servicios:**
    ```bash
    docker-compose up -d
    ```
## 🔐 Configuración de la VPN (WireGuard)

Para conectar tus dispositivos a la red privada del servidor:

1.  **Generar clientes:** En el `docker-compose.yml`, la variable `PEERS=15` define cuántos accesos se crean automáticamente.
2.  **Obtener el código QR:** Ejecuta el siguiente comando en la terminal para ver el QR de un cliente (ej. el cliente 1):
    ```bash
    docker exec -it wireguard /app/show-peer 1
    ```
3.  **Archivos de configuración:** Los archivos `.conf` para PC o Mac se encuentran en la carpeta mapeada: `./wireguard_config/peerX/`.

## 🔐 Notas de Seguridad
* Nunca subas el archivo `.env` al repositorio (está incluido en `.gitignore`).
* Cambia las contraseñas por defecto en el archivo `.env` antes de levantar el stack.

### 📊 Arquitectura del Sistema
```mermaid
graph TD
    User((Usuario/Admin)) -->|Puerto 80/443| Caddy[Caddy Server - SSL Auto]
    User -->|Puerto 51820 UDP| WG[WireGuard VPN]
    
    subgraph "Docker Stack (AWS EC2)"
        Caddy -->|Proxy| n8n[n8n Automation]
        Caddy -->|Serves| PHP[App PHP]
        PHP -->|Query| MySQL[(MySQL 8.0 Database)]
        PMA[phpMyAdmin] -->|Manage| MySQL
        WG -->|Secure Access| InternalNet[Red Interna Docker]
    end
