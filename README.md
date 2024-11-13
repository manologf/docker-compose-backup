# docker-compose-backup
Este manual proporciona los pasos necesarios para la instalación y configuración de un contenedor en Docker Compose que realiza copias de seguridad automáticas de una base de datos MySQL.

---

## 1. Instalación de Docker y Docker Compose
Para ejecutar el contenedor, es necesario tener Docker y Docker Compose instalados. Si estás en una distribución compatible con dnf puedes usar los siguientes comandos:

```bash
sudo dnf -y update
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl enable --now docker
sudo systemctl status docker

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---

## 2. Puesta en marcha del proyecto
Clona el proyecto en tu máquina:

```bash
git clone https://github.com/manologf/docker-compose-backup.git
cd docker-compose-backup
```

---

## 3. Configuración del contenedor
Este proyecto incluye todos los contenedores necesarios. En concreto, el contenedor del respaldo de la base de datos incluye lo siguiente:
- La imagen que se usará, en este caso la más reciente de mysql
- El nombre del contenedor, en este caso "db_backup"
- Las variables de entorno que se usan, tales como el usuario, la contraseña, la contraseña del root y la base de datos usada.
- El directorio donde se realizará el montaje de los respaldos
- El comando en bash, el cual establece la fecha del respaldo, el host (en este caso no usamos dirección IP, sino la base de datos a respaldar), el usuario y la contraseña para el acceso, e indicamos con qué formato se guardarán las copias
- La frecuencia con la que se realizarán las copias de seguridad
- Las dependencias que se necesitan para ejecutar este contenedor, en este caso necesitamos el contenedor de la base de datos activo

Además, incluye un archivo .env en el que se definen las variables de entorno necesarias.

---

## 4. Ejecución del contenedor
Ejecutamos lo siguiente en la terminal para activar el proyecto:

```bash
docker-compose up -d
```
---

### 5. Permisos de la base de datos
Necesitamos que el usuario de la base de datos tenga permiso para acceder a ella y poder crear las copias, para ello:

Entramos al contenedor:
```bash
docker exec -it docker-compose-backup-mysql-1 bash
```
Accedemos a la base de datos:
```bash
mysql -u root -p
```
e introducimos la contraseña de root.

Garantizamos permisos al usuario:
```SQL
GRANT PROCESS ON *.* TO 'user1'@'%';
FLUSH PRIVILEGES;

GRANT ALL PRIVILEGES ON *.* TO 'user1'@'%';
FLUSH PRIVILEGES;
```
---

## 6. Comprobamos el funcionamiento
Se deben revisar los logs generados, aunque también se puede acceder al directorio donde se encuentran las copias:

```bash
docker-compose logs -f db_backup
```

---

## 7. Opciones configurables
Las variables que pueden ser configuradas por el usuario libremente son las siguientes:
- La ruta de almacenamiento del respaldo
- La frecuencia con la que se realizan las copias de seguridad
- El formato con el que se guarda la copia de seguridad
- El formato de la fecha
