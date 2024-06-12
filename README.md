# dockeralp
Docker
1.Создан свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx).
2.Определена разница между контейнером и образом, написан вывод.
3.Написан ответ на вопрос: Можно ли в контейнере собрать ядро?
4.Собранный образ запушин в docker hub и дана ссылка на репозиторий.

1. Создан свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx).  

Зарегистрировался на docker hub, alexxeykz.

Обновим и установим docker:
```
root@docker:~# sudo apt update && sudo apt install docker.io
```
Устанавливаем, если докер не стоял, то выше пункт можно пропустить:

# Add Docker's official GPG key:
```
root@docker:~# sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
Hit:1 http://us.archive.ubuntu.com/ubuntu jammy InRelease
Hit:2 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:3 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu jammy-security InRelease
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20230311ubuntu0.22.04.1).
ca-certificates set to manually installed.
The following packages were automatically installed and are no longer required:
  bridge-utils dns-root-data dnsmasq-base pigz ubuntu-fan
Use 'sudo apt autoremove' to remove them.
The following packages will be upgraded:
  curl libcurl4
2 upgraded, 0 newly installed, 0 to remove and 94 not upgraded.
Need to get 484 kB of archives.
After this operation, 0 B of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 curl amd64 7.81.0-1ubuntu1.16 [194 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libcurl4 amd64 7.81.0-1ubuntu1.16 [290 kB]
Fetched 484 kB in 1s (336 kB/s)
(Reading database ... 44854 files and directories currently installed.)
Preparing to unpack .../curl_7.81.0-1ubuntu1.16_amd64.deb ...
Unpacking curl (7.81.0-1ubuntu1.16) over (7.81.0-1ubuntu1.15) ...
Preparing to unpack .../libcurl4_7.81.0-1ubuntu1.16_amd64.deb ...
Unpacking libcurl4:amd64 (7.81.0-1ubuntu1.16) over (7.81.0-1ubuntu1.15) ...
Setting up libcurl4:amd64 (7.81.0-1ubuntu1.16) ...
Setting up curl (7.81.0-1ubuntu1.16) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.6) ...
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
```

# Add the repository to Apt sources:
```
root@docker:~# echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
Get:1 https://download.docker.com/linux/ubuntu jammy InRelease [48.8 kB]
Hit:2 http://us.archive.ubuntu.com/ubuntu jammy InRelease
Get:3 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages [33.7 kB]
Hit:4 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:5 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:6 http://us.archive.ubuntu.com/ubuntu jammy-security InRelease
Fetched 82.5 kB in 1s (114 kB/s)
Reading package lists... Done
```

Install the Docker packages:
```
root@docker:~# sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  bridge-utils dns-root-data dnsmasq-base ubuntu-fan
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  docker-ce-rootless-extras libltdl7 libslirp0 slirp4netns
Suggested packages:
```
Убедимся, что установка Docker Engine прошла успешно, запустив hello-worldобраз:
```
root@docker:~# systemctl start docker
root@docker:~# sudo docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
Посмотрим запущенные контейнеры
```
root@docker:~# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
Загрузим образ nginx:alpine:
```
root@docker:/# docker pull nginx:alpine
alpine: Pulling from library/nginx
4abcf2066143: Pull complete
b1e69ebc7f92: Pull complete
628158b45bce: Pull complete
346e52e95fa0: Pull complete
8c57fb1cd644: Pull complete
dc3800d1d0f2: Pull complete
e3227d68030d: Pull complete
8c50e1264d11: Pull complete
Digest: sha256:69f8c2c72671490607f52122be2af27d4fc09657ff57e42045801aa93d2090f7
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine

Проверим наличие образа:
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         alpine    70ea0d8cc530   11 days ago     48.3MB
hello-world   latest    d2c94e258dcb   13 months ago   13.3kB

Запустим контейнер:
root@docker:/# docker run --name alexxeykz-nginx -p 8080:80 -d nginx:alpine
7f1d053879d8ccef6ec7641b6afb5d5d44e7a2df5dd053e0662c70a404ad7940

Проверим запущенные контейнеры
root@docker:/# docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                      PORTS                                   NAMES
7f1d053879d8   nginx:alpine   "/docker-entrypoint.…"   37 seconds ago   Up 37 seconds               0.0.0.0:8080->80/tcp, :::8080->80/tcp   alexxeykz-nginx
0ed256c39dbe   hello-world    "/hello"                 35 minutes ago   Exited (0) 35 minutes ago                                           pensive_keldysh
cdcf13599257   hello-world    "/hello"                 54 minutes ago   Exited (0) 54 minutes ago                                           keen_brahmagupta

Проверим работу nginx:
```
root@docker:/# curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
Посмотрим содержимое контейнера:
```
root@docker:/# docker exec -it alexxeykz-nginx ls
bin                   docker-entrypoint.d   etc                   lib                   mnt                   proc                  run                   srv                   tmp                   var
dev                   docker-entrypoint.sh  home                  media                 opt                   root                  sbin                  sys                   usr
```
По умолчанию страница находится /usr/share/nginx/html/index.html
Изменим на свою:
```
root@docker:/# docker rm -f alexxeykz-nginx
root@docker:/dockerapp# docker run --name alexxeykz-nginx -p 8080:80 -v /nginx/:/usr/share/nginx/html -d nginx:alpine
root@docker:/dockerapp# curl localhost:8080
<html>
  <body>
    <h1>alexxeykz.ru</h1>
    <p>
      <a href="https://www.topbicycle.ru/m/russian/">
        alexxeyk_Site
      </a>
      <br><br>
      <a href="https://www.topbicycle.ru/m/italian/">
        Welcome!!!!
      </a>
    </p>
  </body>
</html>
```
Изменили с дефолтной на свою страницу.

2. Отвечая на вопрос разница образа и контейнера. 
Образ - это шаблон, на основе которого создается контейнер.
Контейнер - это среда в которой пользователи могут изолировать приложения от хостовой системы. Эти контейнеры представляют собой компактные портативные хосты.

3.Написан ответ на вопрос: Можно ли в контейнере собрать ядро?
В Docker-контейнере можно собрать ядро с произвольными патчами, флагами конфигурации и тегом.

4.Собранный образ запушин в docker hub и дана ссылка на репозиторий.
```
root@docker:/dockerapp# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
nginx         alpine    70ea0d8cc530   13 days ago     48.3MB
hello-world   latest    d2c94e258dcb   13 months ago   13.3kB
```

Для того чтобы запушить нам сначала нужно присвоить TAG
root@docker:/dockerapp# docker container ls -a
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS                    PORTS                                   NAMES
075dce404115   nginx:alpine   "/docker-entrypoint.…"   9 hours ago    Up 9 hours                0.0.0.0:8080->80/tcp, :::8080->80/tcp   alexxeykz-nginx
0ed256c39dbe   hello-world    "/hello"                 33 hours ago   Exited (0) 33 hours ago                                           pensive_keldysh
cdcf13599257   hello-world    "/hello"                 33 hours ago   Exited (0) 33 hours ago                                           keen_brahmagupta

root@docker:/dockerapp# docker commit alexxeykz-nginx alexxeykz/alexxeykz-nginx:alpng
sha256:51c77fe314c07420afcf2306c6306e296eeb23dc589800a6816a9c5e8a6c7f89

Смотрим images:
```
root@docker:/dockerapp# docker images
REPOSITORY                  TAG       IMAGE ID       CREATED          SIZE
alexxeykz/alexxeykz-nginx   alpng     51c77fe314c0   27 seconds ago   48.3MB
nginx                       alpine    70ea0d8cc530   13 days ago      48.3MB
hello-world                 latest    d2c94e258dcb   13 months ago    13.3kB
```

Далее пушим, предварительно авторизовавшись на docker hub(docker login):
```
root@docker:/dockerapp# docker push alexxeykz/alexxeykz-nginx
Using default tag: latest
The push refers to repository [docker.io/alexxeykz/alexxeykz-nginx]
tag does not exist: alexxeykz/alexxeykz-nginx:latest
```
Здесь указано что я не указал tag после названия образа, указываем его:
```
root@docker:/dockerapp# docker push alexxeykz/alexxeykz-nginx:alpng
The push refers to repository [docker.io/alexxeykz/alexxeykz-nginx]
1a48b0a13ff5: Pushed
9cba8117003a: Mounted from library/nginx
b6d04dc5ecf7: Mounted from library/nginx
d38ed9b519d2: Mounted from library/nginx
3b4115e2edd1: Mounted from library/nginx
8d720e2faad3: Mounted from library/nginx
7b87df18a0ed: Mounted from library/nginx
a05d3326ce5a: Mounted from library/nginx
d4fc045c9e3a: Mounted from library/nginx
alpng: digest: sha256:2548759acbcd2c5efadcc73e31324c1b58bd834dafd5d2b39e8dd9043b0fd648 size: 2196
```
Все, он в нашем репозитарии.

Теперь данный образ можно закачать:
```
docker pull alexxeykz/alexxeykz-nginx
```




















