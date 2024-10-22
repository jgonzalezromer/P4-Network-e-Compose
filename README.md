# P4-Network-e-Compose

## Docker network

### Crear unha rede en docker
Para crear unha rede en docker utilizamos o comando:

```
docker network create<nome_da_rede>
```

### Crear dous contenedores unidos a esa rede
Para crear dous contenedores nesa rede utilizaremos o comando
```
docker run -itd --name=<nome_do_contenedor> --network=<nome_da_network>
```
### Comprobar que os contenedores están na rede
Para ver os detalles dunha rede, onde poderemos encontrar un apartado de "Containers" onde estarán os contenedores que están ligados a esa rede, utilizamos o comando:
```
docker network inspect <nome_da_rede>
```
### Comprobar que os contenedores poden verse entre eles

### Listar os contenedores conectados á rede

### Listar as propiedades da rede

### Crea outra rede

### Lanza dous contenedores novos conectados a esa nova rede

### Comproba as posibles conexións entre os 4 contenedores

## Docker compose:

### Segue os pasos da guía de iniciación de docker-compose, e explica coas túas palabras os pasos que segues e qué fan
a
### Agora que sabes algo máis de docker-compose, crea un arquivo (ou varios arquivos) de configuración que ó ser lanzados cun docker-compose up, resulten nunha rede docker á que estean conectados 3 contenedores, explica os parámetros do .yaml usado

### Busca e proba 4 parámetros e configuracións diferentes que podes incluir no arquivo compose, explica qué fan. (por exemplo diferentes cousas que facer coa opción RUN)