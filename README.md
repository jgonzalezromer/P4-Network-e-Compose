<h1>
<p align=center>
P4-Network-e-Compose 
</p>
</h1>
<h3>
<p align=center>
Juan Gabriel González Romero
</p>
</h3>

---
## Docker network
---
### Crear unha rede en docker
Para crear unha rede en docker utilizamos o comando:

```
docker network create<nome_da_rede>
```
---
### Crear dous contenedores unidos a esa rede
Para crear dous contenedores nesa rede utilizaremos o comando
```
docker run -itd --name=<nome_do_contenedor> --network=<nome_da_network>
```
---
### Comprobar que os contenedores están na rede
Para ver os detalles dunha rede, onde poderemos encontrar un apartado de "Containers" onde estarán os contenedores que están ligados a esa rede, utilizamos o comando:
```
docker network inspect <nome_da_rede>
```
---
### Comprobar que os contenedores poden verse entre eles
Para saber se os contenedores se ven entre eles debemos hacer ping, para iso entraremos nunha terminal, facendo ping, se non o temos podemos descargar o paquete nettools,co comando:
```
docker exec -it <nome_do_contenedor> sh
```
---
### Listar os contenedores conectados á rede
Para ver os detalles dunha rede, onde poderemos encontrar un apartado de "Containers" onde estarán os contenedores que están ligados a esa rede, utilizamos o comando:
```
docker network inspect <nome_da_rede>
```
---
### Listar as propiedades da rede
Para ver os detalles dunha rede utilizamos o comando:
```
docker network inspect <nome_da_rede>
```
---
### Crea outra rede
Para crear unha rede en docker utilizamos o comando:

```
docker network create<nome_da_rede>
```
---
### Lanza dous contenedores novos conectados a esa nova rede
Para crear dous contenedores nesa rede utilizaremos o comando
```
docker run -itd --name=<nome_do_contenedor> --network=<nome_da_network>
```
---
### Comproba as posibles conexións entre os 4 contenedores
Como os contenedores están en distintas redes 2 a 2, estes so poderan ver os contenedores que están na sua rede.
Para saber se os contenedores se ven entre eles debemos hacer ping, para iso entraremos nunha terminal, facendo ping, se non o temos podemos descargar o paquete nettools,co comando:
```
docker exec -it <nome_do_contenedor> sh
```

---

## Docker compose:
---
### Segue os pasos da guía de iniciación de docker-compose, e explica coas túas palabras os pasos que segues e qué fan

O primeiro que faremos será descargar docker compose isto o faremos co comando:
```
sudo apt install docker-compose
```
Agora crearemos unha carpeta onde irá o noso proxecto e entraremos nela:
```
mkdir <nome_da_carpeta> && cd <nome_da_carpeta>
```
Nesta ubicación crearemos un arquivo chamado app.py, o cal estará escrito en python, e copiaremos o seguinte codigo:
```
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```
O seguinte será crear un arquivo chamado requirements.txt e copiar o seguinte codigo:
```
flask
redis
```
Logo crearemos un Dockerfile y copiaremos el siguiente codigo:
```
# syntax=docker/dockerfile:1
FROM python:3.10-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run", "--debug"]
```
Con estos arquivos creados, agora crearemos un arquivo chamado compose.yaml, o cal define dous servizos web e redis, co seguinte codigo:
```
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
```
Agora montaremos e arrancaremos a app con compose, para iso nunha terminal faremos o comando:
```
docker compose up
```
Se todo foi ben, agora se buscamos nun navegador `http://localhost:8000/` aparecerá o texto "Hello World! I have been seen X times." e se volvemos a terminal podremos ver as conexións cun texto como este:
```
eb-1    |  * Serving Flask app 'app.py'
web-1    |  * Debug mode: on
web-1    | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
web-1    |  * Running on all addresses (0.0.0.0)
web-1    |  * Running on http://127.0.0.1:5000
web-1    |  * Running on http://172.20.0.3:5000
web-1    | Press CTRL+C to quit
web-1    |  * Restarting with stat
web-1    |  * Debugger is active!
web-1    |  * Debugger PIN: 526-560-688
web-1    | 172.20.0.1 - - [15/Oct/2024 19:02:19] "GET /P2.html HTTP/1.1" 404 -
web-1    | 172.20.0.1 - - [15/Oct/2024 19:02:19] "GET /favicon.ico HTTP/1.1" 404 -

```
---
### Agora que sabes algo máis de docker-compose, crea un arquivo (ou varios arquivos) de configuración que ó ser lanzados cun docker-compose up, resulten nunha rede docker á que estean conectados 3 contenedores, explica os parámetros do .yaml usado

Para facer isto vamos a reciclar a carpeta que xa fixemos antes e borraremos reescribiremos o compose.yaml con un codigo como este:
```
services:  # Define os servizos (contenedores)
  
  <Nome_do_contedor>:
    image: <Imaxe_a_utilizar>
    container_name: <Nome_especifico>
    networks: 
      - <Nome_da_rede>
    command: tail -f /dev/null #Este comando fai que se mantenga activo o contenedor
  <Nome_do_contedor>:
    image: <Imaxe_a_utilizar>
    container_name: <Nome_especifico>
    networks: 
      - <Nome_da_rede>
    command: tail -f /dev/null #Este comando fai que se mantenga activo o contenedor
  <Nome_do_contedor>:
    image: <Imaxe_a_utilizar>
    container_name: <Nome_especifico>
    networks: 
      - <Nome_da_rede>
    command: tail -f /dev/null #Este comando fai que se mantenga activo o contenedor

networks:  # Definición das redes
  <nome_da_rede>:  # Nome da rede que estamos a crear
    driver: <tipo_de_rede>  
```
Se todo foi ben debería devolver a terminal isto:
```
gabriel@gabriel-VirtualBox:~/proxecto0$ docker compose up
WARN[0000] Found orphan containers ([proxecto0-web-1 proxecto0-redis-1]) for this project. If you removed or renamed this service in your compose file, you can run this command with the --remove-orphans flag to clean it up. 
[+] Running 4/4
 ✔ Network proxecto0_rede01  Crea...                            0.0s 
 ✔ Container proxecto02      Created                            0.1s 
 ✔ Container proxecto03      Created                            0.1s 
 ✔ Container proxecto01      Created                            0.1s 
Attaching to proxecto01, proxecto02, proxecto03
 
```
---
### Busca e proba 4 parámetros e configuracións diferentes que podes incluir no arquivo compose, explica qué fan. (por exemplo diferentes cousas que facer coa opción RUN)

O primeiro parámetro que vamos a ver é `environment` o cal permite establecer variables de contorno para un contenedor. Como por exemplo configurar a conexión a base de datos, portos,...
Un código posible podería ser:
```
services:
  web:
    image: "nginx:latest"
    environment:
      - NGINX_HOST=localhost
      - NGINX_PORT=80
```
O segundo parámetro é `volumes` o cal permietenos mapear directorios ou ficheros do sistema anfitrión ou contedor. Un exemplo sería:
```
services:
  web:
    image: "nginx:latest"
    volumes:
      - ./html:/usr/share/nginx/html
```
O terceiro parámetro é `ports`, que nos permite expoñer portos do contedor ao sistema anfitrión.
Isto fai que o servizo sexa accesible dende o sistema anfitrión ou dende outros dispositivos na rede. Un exemplo sería:
```
services:
  web:
    image: "nginx:latest"
    ports:
      - "8080:80"
```
O cuarto parámetro é `depends_on`, o cal asegura que uns servizos comecen antes que outros.
Úsase para definir dependencias entre servizos. Un exemplo sería:
```
services:
  db:
    image: "postgres:latest"
  web:
    image: "nginx:latest"
    depends_on:
      - db
```

---