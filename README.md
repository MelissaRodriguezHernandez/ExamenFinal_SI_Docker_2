# Examen final de Sistemas Infórmaticos : Despliegue de aplicación en Docker (Versión 2)

1. [Introducción](#intro)
2. [Configuración del archivo docker-compose.yml](#conf)
3. [Pasos para el despliegue de la aplicación](#apli)
4. [Preparación y subida de la imagen](#img)
5. [Conclusiones](#con)
6. [Annexos](#anex)

<div id= 'intro'>

## Introducción
  
> Esta práctica se basara en el despliegue de nuestro proyecto en docker, mediante el docker compose, haciendo uso de : mysql, phpmyadmin y tomcat.
  Finalmente crearemos una imagen a partir del despliegue y lo subiremos a DockerHub.
  
</div>

<div id= 'conf'>

## Configuración del archivo docker-compose

El archivo docker compose es un archivo YML donde definiremos los servicios, redes y volúmenes. Lo colocaremos en dentro del directorio junto con todo lo necesario para armar el ambiente. Nuestro docker compose tendra lo necesario para ejecutar el mysql, tomcat y phpmyadmin. Es el siguiente:

  ```
 version: '3.3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_vol:/var/lib/mysql
       - ./mysql-dump:/docker-entrypoint-initdb.d
     environment:
       MYSQL_ROOT_PASSWORD: root
       MYSQL_DATABASE: testdb1
       MYSQL_USER: testuser
       MYSQL_PASSWORD: root
     ports:
       - 3306:3306
   phpmyadmin:
    depends_on:
      - db
    image: phpmyadmin/phpmyadmin
    ports:
      - '8081:80'
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: root
   web:
    build:
      context: .       
    depends_on:
      - db
    image: tomcat
    volumes:
            - ./target/LoginWebApp.war:/usr/local/tomcat/webapps/LoginWebApp.war
    ports:
      - '8082:8080'
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: testdb1
      MYSQL_USER: testuser
      MYSQL_PASSWORD: root
volumes:
    db_vol:      

  ```
  
</div>

<div id= 'apli'>

## Pasos para el despliegue de la aplicación

Una vez hemos creado el docker-compose.yml, en el mismo directorio deberemos tener todos los archivos necesarios. 
  
  - El archivo del proyecto en un .war (el nuestro esta en la carpeta target)
  
  - El archivo de la base de datos .sql (el nuestro esta en la carpeta mysql-dump)

Con todo esto, vamos a la terminal y nos situamos en el directorio donde se encuentra todo, y ejecutamos el siguiente comando:
  
```
  sudo docker-compose up -d
  ```
![compose](https://user-images.githubusercontent.com/91748294/173110932-ea20c331-be48-4ce5-b9f6-08249d585acc.png)


Ahora podremos ir a las páginas. Si vamos al localhost:8080 veremos como se despliega la página principal y como podemos registarnos y loguearnos.
  
![inicioPagina](https://user-images.githubusercontent.com/91748294/173114900-6498de8c-7e9d-4b64-b5f2-4320d49b1988.png)

 ![registration succel](https://user-images.githubusercontent.com/91748294/173115014-505c8291-9c33-4a8b-abb8-ff4d84d6ea09.png)

  ![incio](https://user-images.githubusercontent.com/91748294/173115031-a5f1e2a2-bc5f-429b-9bea-5c0813a889b1.png)

 También si vamos a localhost:8081 podremos loguearnos en el phpmyadmin (con el usuario y contraseña que definimos en el docker-compose) y mirar la base de datos.
  
![inicioPHP](https://user-images.githubusercontent.com/91748294/173115058-4630ab57-365c-4f29-a658-0cb785fa59fd.png)

![phpBases](https://user-images.githubusercontent.com/91748294/173115071-32806f9b-25ca-4f99-a8de-ce81fd09cd2e.png)
  
</div>

<div id= 'img'>

## Preparación y subida de la imagen
  
Ahora con nuestro proyecto desplegado procederemos a crear una imagen y contenedor, para luego subirlo en el DockerHub.
  
Empezaremos por crear el Dockerfile

```
FROM tomcat:latest

LABEL maintainer="Maximo Fernandez"

ADD ./target/LoginWebApp.war /usr/local/tomcat/webapps/

EXPOSE 8080

CMD ["catalina.sh", "run"]

  ```
Y ejecutamos el comando para monstar la imagen:

  ```
  docker build -t mely1h1r/loginweb . 
 
  ```
![build](https://user-images.githubusercontent.com/91748294/173115475-06a96235-f716-4fba-87b2-a7d76c43c708.png)


Ahora procederemos a subirlo al dockerHub
  
  ```
docker push mely1h1r/loginweb
  ```
 ![push](https://user-images.githubusercontent.com/91748294/173115464-9d9cec2e-75a3-448d-9481-457adc954a43.png)


  
Si todo ha salido bien nos saldría algo así, con esto ya podriamos bajarnos el contenedor en cualquier ordenador y ejecutarlo:
  
![hub](https://user-images.githubusercontent.com/91748294/173115455-0917931b-7dbb-4623-8f92-eade70bc8a05.png)

(Link en el annexo)
  
</div>

<div id= 'con'>

## Conclusiones
  
</div>

<div id= 'anex'>

## Annexo
  
[Link del contendor en DockerHub](https://hub.docker.com/r/mely1h1r/loginweb)
  
</div>
