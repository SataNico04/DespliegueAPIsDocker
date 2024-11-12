# Documentación para desplegar APIs usando Docker

## Laravel

Lo primero que tenemos que hacer es descargarnos la API del siguiente repositorio de [Laravel](https://github.com/SataNico04/PPS-Despliege-Laravel-G4).

![alt text](image-2.png)

En el repositorio ya se encuentran todos los archivos necesarios para la creación del docker, lo único que falta es el .env, que guarda información confidencial de la API. Podemos crear un nuevo .env a partir del .env.example.

Podrás notar que el repositorio tiene unos archivos/directorios *extra*, como las carpetas mysql-init y nginx, y los ficheros docker-compose.yml y Dockerfile.

Vamos a verlos con más detalle:

- Dockerfile.
  Crea un Docker con PHP y las dependencias necesarias para poder ejecutar Laravel.
  (Instalar paquetes, copiar carpetas a la carpeta de trabajo y cambiar permisos).

- docker-compose.yml.
  Crea los servicios que va a utilizar la API de Laravel. Una app, o contenedor principal de la app, que incluye todos los ficheros del directorio donde se encuentra, un nginx que va a alojar la API y usará los archivos de la carpeta **nginx/** y una base de datos Mysql que guardará todas las peticiones POST.

- mysql-init.
  En esta carpeta se encuentra un archivo .sql, que ejecuta un comando de base de datos que, si existe la base de datos, la borra y crea una nueva.

- nginx.
  Contiene un default.conf, un archivo necesario para la configuración del **nginx**

Por último, para desplegarlo, debemos poner los siguientes comandos en la consola:

```bash
docker compose build
docker compose up -d
```

Ahora, podríamos entrar en la siguiente URL: http://localhost:8000/numeros, nos dará un pequeño error, ya que no hemos aplicado las migraciones, no pasa nada, le damos clic al botón verde donde pone "Run Migrations", y si recargamos:

![alt text](image-4.png)

Nos aparecerá un json vacío. Si hacemos un POST usando *curl*:

```bash
curl -X POST http://localhost:8000/numeros -H "Content-Type: application/json" -d '{"numero": 5}'
```

![alt text](image-5.png)

---

## Springboot

Nos descargamos el contenido del siguiente repositorio [Springboot](https://github.com/IES-Rafael-Alberti/PPS-despliegue-springboot-g4). 

![alt text](image-1.png)

En la carpeta *demo/demo*, encontramos un Dockerfile, el cual contiene 2 etapas:
1. Etapa de construcción.
   Usa una imagen de Gradle para compilar la aplicación Java, generando un archivo .jar en el directorio build/libs.

2. Etapa de ejecución.
   Usa una imagen ligera de Java para ejecutar el archivo .jar generado en la etapa anterior, exponiendo el puerto 8080 para acceder a la aplicación.

Para desplegar el docker ponemos los siguientes comandos:

```bash
docker build -t springboot-app .
docker run -p 8080:8080 springboot-app
```

De esta forma, la API ya estaría desplegada en la siguiente URL: http://localhost:8080/.

---

## FastApi

Lo primero que haremos es clonar el repositorio de [FastAPI](https://github.com/IES-Rafael-Alberti/PPS-despliegue-fastapi-g4). 

![alt text](image.png)

Una vez en nuestro sistema, añadimos el siguiente archivo Dockerfile:

```Dockerfile
FROM python:3.11-slim

WORKDIR /code

# Copiar requirements.txt
COPY requirements.txt .

# Instalar dependencias
RUN pip install --no-cache-dir --upgrade -r requirements.txt

# Copiar el resto de los archivos y carpetas
COPY app.py .
COPY data/ ./data/
COPY models_module/ ./models_module/
COPY test.db .

# Comando para ejecutar la aplicación
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

Este Dockerfile:

- Usa una imagen base de Python 3.11 ligera
- Establece el directorio de trabajo
- Copia e instala las dependencias
- Copia el código de la aplicación
- Configura el comando para ejecutar la aplicación con Uvicorn

Modificación del archivo requirements.txt (para forzar que sean exactamente esa versión o dará error):

        fastapi==0.115.4
        uvicorn==0.22.0
        sqlalchemy==2.0.15
        databases==0.9.0

Construcción de la imagen Docker
Abre una terminal en el directorio del proyecto y ejecuta:

  docker run -p 80:80 fastapi-app

Este comando construirá la imagen Docker con el nombre "mifastapi"

Ejecución del contenedor

Para ejecutar la aplicación en un contenedor, usa:

  docker run -d -p 80:80 fastapi-app

Este comando:

- Ejecuta el contenedor en segundo plano (-d)
- Le asigna un nombre al contenedor
- Mapea el puerto 80 del contenedor al puerto 80 del host

![alt text](image-3.png)