# Docker

## Correction

### Exercice 1

```bash
docker run -it --name ctr1 nginx bash

echo "Welcome to Hexagone" > /usr/share/nginx/html/index.html

docker commit ctr1 mon-site

docker run -d -p 80:80 mon-site

curl localhost
```

### Exercice 2

Dockerfile :

```Dockerfile
FROM nginx:1.20.2
RUN apt update
RUN apt install -y vim nano
```

Commande pour builder

```bash
docker build -t nginx-dev:1
```

### Exercice 3

```bash
echo 'coucou' > index.html
```

```Dockerfile
FROM nginx:1.20.2
COPY index.html /usr/share/nginx/html/index.html
```

```bash
docker build -t exo3 .
docker run -d -p80:80 exo3
```

### Exercice 4

``` Dockerfile

FROM nginx:1.20.2

ADD https://www.docker.com /usr/share/nginx/html/index.html

RUN chmod 0777 /usr/share/nginx/html/index.html
```

```bash

docker build -t exo4 .

docker run -d -p8080:80 exo4

curl localhost:8080
```

### Exercice 5

```Dockerfile
FROM alpine
WORKDIR /tmp
RUN adduser -D dev
ADD --chown=dev https://get.docker.com script.sh
COPY --chown=dev Dockerfile /tmp
USER dev
```

```bash
docker build -t exo5 .
docker run -it exo5 sh
cat script.sh
cat Dockerfile
```

### Exercice 6

```Dockerfile
FROM alpine:latest
RUN apk add curl
ENTRYPOINT ["curl"]
```

```bash
docker build -t exo6 .
docker run -it exo6
```

### Exercice 7

```Dockerfile
FROM alpine
ENV PROD=false
CMD echo $PROD
```

```bash
docker build -t exo7 .
docker run -it --rm exo7
```

### Exercice 8

```bash

docker volume create data

docker container run -d -p 80:80 -v data:/usr/share/nginx/html --name ctr1 nginx

docker container run -d --name ctr2 -v data:/data nanabak/nginx-dev:1

docker container exec -it ctr2 bash

echo "Hello world" >/data/index.html

# Sortir de notre conteneur ctr2 avec ^C ou exit
docker cp ctr2:/data/index.html .

# Suppression du conteneur ctr1
docker container stop ctr1
docker container rm ctr1

# Mise à jour version nginx à stable

docker container run -d -p 80:80 --name ctr1 nginx:stable

```

### Exercice 9

```bash
docker run --name exo9 -it --tmpfs /hexagone_tmp -v data:/hexagone_data nginx bash

curl -Lo /hexagone_tmp/script.sh https://get.docker.com
curl -Lo /hexagone_data/script.sh https://get.docker.com

exit

docker stop exo9
docker start exo9

docker exec -it exo9 bash

ls /hexagone_tmp
ls /hexagone_data
```

### Exercice 10

```bash

# pour les mac, on mettra un $(pwd)
docker container run -d -v data:/data -tmpfs /data_tmp \
-v ${pwd}:/data_bind --name ctr1 alpine

docker container exec -it ctr1 sh

touch  /data/hexagone.txt /data_bind/hexagone.txt /data_tmp/hexagone.txt

ls /data_{bind, tmp} /data

docker container run -it -v data:/data -tmpfs /data_tmp \
-v ${pwd}:/data_bind --name ctr2 alpine sh

ls /data_{bind, tmp} /data

#on constatera que le dossier data_tmp est vide dans le ctr2
```

### Exercice 11

```yaml
version: "3"

services:
   web:
       image: nginx
       ports:
        - "80:80"
       volumes:
        - data:/usr/share/nginx/html/index.html
   container:
       image: nanabak/nginx-dev:1
       volumes:
         - data:/data

volumes:
   data:
```

### Exercice 12

```Dockerfile
FROM nginx:stable
RUN apt update
RUN apt install -y vim nano
```

```yaml
version: "3"

services:
    web:
        image: nginx
        ports:
            - "80:80"
        volumes:
            - data:/usr/share/nginx/html/index.html
    container:
        build: .
        volumes:
            - data:/data
        entrypoint: ""
        command: /bin/sh -c "echo coucou > /data/index.html"
volumes:
   data:

```

### Exercice 13

```yaml
version: "3"

services:
  web:
    image: nginx:stable
    ports:
      - "80:80"
    volumes:
      - data:/usr/share/nginx/html
  dev:
    #    image: nanabak/nginx-dev:1
    build: .
    image: nanabak/nginx-dev:1
    entrypoint: ""
    command: /bin/sh -c "echo $$PROD > /data/index.html"
    environment:
      PROD: "true"
    volumes:
      - data:/data
      - type: bind
        source: .
        target: /data_bind

    tmpfs:
      - /data_tmp
volumes:
  data:

```
