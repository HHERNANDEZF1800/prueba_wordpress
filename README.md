# WordPress con Docker

Este proyecto configura un entorno completo de WordPress utilizando Docker Compose.

## Servicios incluidos

- **WordPress**: Aplicación principal de WordPress
- **MySQL 8.0**: Base de datos para WordPress
- **phpMyAdmin**: Interfaz web para gestionar la base de datos

## Requisitos previos

- Docker instalado en tu sistema
- Docker Compose instalado

## Instrucciones de instalación

### 1. Clonar o descargar el proyecto

Si aún no tienes los archivos, asegúrate de tener `docker-compose.yml` en tu directorio de trabajo.

### 2. Configurar variables de entorno

Copia el archivo de ejemplo `.env.example` a `.env`:

```bash
cp .env.example .env
```

Edita el archivo `.env` y cambia las contraseñas por unas seguras:

```bash
nano .env
# o usa tu editor preferido: vim, code, etc.
```

**Variables importantes a modificar:**
- `MYSQL_ROOT_PASSWORD`: Contraseña del usuario root de MySQL
- `MYSQL_PASSWORD`: Contraseña del usuario de WordPress para MySQL
- `WORDPRESS_DB_PASSWORD`: Debe ser igual a `MYSQL_PASSWORD`

### 3. Iniciar los contenedores

Ejecuta el siguiente comando en el directorio del proyecto:

```bash
docker-compose up -d
```

Este comando descargará las imágenes necesarias (si no las tienes) y creará los contenedores en segundo plano.

### 4. Verificar que los contenedores están corriendo

```bash
docker-compose ps
```

Deberías ver tres contenedores activos: `wordpress_app`, `wordpress_db` y `wordpress_phpmyadmin`.

### 5. Acceder a los servicios

- **WordPress**: http://localhost:8080
- **phpMyAdmin**: http://localhost:8081

### 6. Completar la instalación de WordPress

1. Abre tu navegador y ve a http://localhost:8080
2. Selecciona el idioma
3. Completa el formulario con:
   - Título del sitio
   - Nombre de usuario (admin)
   - Contraseña
   - Tu correo electrónico
4. Haz clic en "Instalar WordPress"

## Comandos útiles

### Ver logs de los contenedores

```bash
# Ver logs de todos los servicios
docker-compose logs -f

# Ver logs solo de WordPress
docker-compose logs -f wordpress

# Ver logs solo de MySQL
docker-compose logs -f db
```

### Detener los contenedores

```bash
docker-compose stop
```

### Reiniciar los contenedores

```bash
docker-compose restart
```

### Detener y eliminar los contenedores (los datos persisten)

```bash
docker-compose down
```

### Detener, eliminar contenedores y eliminar volúmenes (SE PERDERÁN TODOS LOS DATOS)

```bash
docker-compose down -v
```

### Acceder a la terminal de un contenedor

```bash
# WordPress
docker-compose exec wordpress bash

# MySQL
docker-compose exec db bash
```

### Hacer backup de la base de datos

```bash
docker-compose exec db mysqldump -u wordpress -p wordpress > backup.sql
```

### Restaurar backup de la base de datos

```bash
docker-compose exec -T db mysql -u wordpress -p wordpress < backup.sql
```

## Estructura de archivos

```
.
├── docker-compose.yml    # Configuración de Docker Compose
├── .env                  # Variables de entorno (no versionar)
├── .env.example         # Ejemplo de variables de entorno
├── .gitignore           # Archivos a ignorar en git
└── README.md            # Este archivo
```

## Volúmenes persistentes

Los datos se guardan en volúmenes de Docker:

- `wordpress_data`: Archivos de WordPress (temas, plugins, uploads)
- `db_data`: Datos de la base de datos MySQL

Estos volúmenes persisten incluso si detienes o eliminas los contenedores (a menos que uses `docker-compose down -v`).

## Personalización de puertos

Si los puertos 8080 u 8081 están ocupados, puedes cambiarlos editando el archivo `.env`:

```env
WORDPRESS_PORT=9000
PHPMYADMIN_PORT=9001
```

Luego reinicia los contenedores:

```bash
docker-compose down
docker-compose up -d
```

## Seguridad

**IMPORTANTE**: Este setup es para desarrollo local. Para producción:

1. Cambia todas las contraseñas por unas fuertes
2. No expongas phpMyAdmin públicamente
3. Usa HTTPS con certificados SSL
4. Configura un firewall adecuado
5. Mantén actualizadas las imágenes de Docker

## Solución de problemas

### Error: "Port is already allocated"

Si recibes este error, otro servicio está usando los puertos 8080 u 8081. Cambia los puertos en el archivo `.env`.

### Error de conexión a la base de datos

1. Verifica que el contenedor de MySQL esté corriendo: `docker-compose ps`
2. Revisa los logs: `docker-compose logs db`
3. Asegúrate de que las credenciales en `.env` coincidan

### WordPress no guarda cambios

Verifica los permisos del volumen:

```bash
docker-compose exec wordpress chown -R www-data:www-data /var/www/html
```

## Recursos adicionales

- [Documentación oficial de WordPress](https://wordpress.org/support/)
- [Documentación de Docker](https://docs.docker.com/)
- [Documentación de Docker Compose](https://docs.docker.com/compose/)

## Licencia

Este proyecto es de código abierto y está disponible bajo la licencia MIT.
