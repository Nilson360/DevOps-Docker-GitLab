# Gitlab

## Correction

### Exercice 1

#### git clone avec docker

Unix/linux

```bash
docker run --rm --name repo -v $(pwd):/git alpine/git clone https://gitlab.com/hexagone-it3-versailles-2022/documentation.git
```

Windows

```bash
docker run --rm --name repo -v ${pwd}:/git alpine/git clone https://gitlab.com/hexagone-it3-versailles-2022/documentation.git
```

#### Builder le dossier public

Unix/linux

```bash
cd documentation
docker run -it --rm -v $(pwd):/data python:latest bash
cd /data
pip install mkdocs-material
mkdocs build --site-dir public
exit
```

#### Déploiement du site web avec un container nginx

```bash
docker run -d -p 80:80 -v $(pwd)/public:/usr/share/nginx/html/ nginx:stable
```
