# microservices - quick guide  

This seems to be a bug in preview build of Windows 10. I got it to work by simply providing the -Force parameter.  

Setting for Local Machine: Set-ExecutionPolicy RemoteSigned -Force  
--> Setting for Current User: Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force  
--> .\add-firewall-rules-for-sts-auth-thru-docker.ps1  

DOCKER  

docker run --name repo alpine/git clone \  
  https://github.com/docker/getting-started.git  
docker cp repo:/git/getting-started/ .  

cd getting-started  
docker build -t docker101tutorial .  

docker run -d -p 80:80 \  
  --name docker-tutorial docker101tutorial  

docker tag docker101tutorial {userName}/docker101tutorial  
docker push {userName}/docker101tutorial  

docker --version  
docker info  

docker run --name whats-up hello-world  

docker ps  
docker ps -a  
docker inspect container_id (or name)  
docker rename whats-up sup  
docker rm -f sup  
docker container prune  

docker run -it ubuntu  
ls -lac  
cat /etc/lsb-release  
exit  -- al apagar el main process, el contenedor se apaga  

docker run --name alwaysup -detach ubuntu tail -f /dev/null  
docker exec -it alwaysup bash  
ll  
ps -aux  
docker inspect --format '{{.State.Pid}}' alwaysup  
kill -9 process_id  -- linux  

docker run --name proxy -d -p 8080:80 nginx  
docker stop proxy  
docker logs --tail 10 -f proxy  

docker run -d --name db mongo  
mongo  
show dbs  
use my-db  
db.users.insert({"name":"my-name"})  
db.users.find()  
exit  
docker run -d --name -v my-pwd-path:/data/db mongo  

docker volume ls  
docker volume create volume_name  
docker run -d --name db --mount src=volume_name,dst=/data/db mongo  

mkdir testing  
docker cp prueba.txt copytest:/testing/test.txt  
docker cp copytest:/testing localtesting  -- no es necesario que el contenedor esté corriendo  

-- construcción y distribución  

docker image ls  
docker pull ubuntu:20.04

d o c k e r f i l e  
FROM ubuntu:latest  
RUN touch /usr/src/hola-mundo.txt  -- misma capa

docker build -t ubuntu:name .   
docker login  
docker tag ubuntu:name user_name/ubuntu:platzi  
docker push user_name/ubuntu:platzi  

docker history ubuntu:name  
https://github.com/wagoodman/dive  
dive ubuntu:name  
https://docs.docker.com/engine/reference/commandline/commit/  

-- desarrollo  

https://github.com/cris-her/docker  
docker run --rm -p 6006:6006 name_of_image  

d o c k e r f i l e - caché  
FROM node:12  
COPY ["package.json", "package-lock.json", "/usr/src/"]  
WORKDIR /usr/src  
RUN npm install  
COPY [".", "/usr/src/"]  
EXPOSE 3000  
CMD ["npx", "nodemon", "index.js"]  

docker run --rm -p 3000:3000 -v path/index.js:/usr/srcindex.js my-app  -- bindmount  

docker network ls  
docker network create --attachable my-net  
docker network inspect my-net  
docker network connect my-net db  
docker run -d --name app -p 3000:3000 --env MONGO_URL=mongodb://db:27017/test my-app  
 
docker-compose up -d  
docker-compose logs -follow service_name1 service_name2  
docker-compose exec app bash -- it no es necesario con docker compose  
docker-compose ps  
docker-compose down  
docker-compose stop service_name1  
docker-compose start service_name1  

d o c k e r - c o m p o s e . y m l  
version: "3.8"  

services:  
  app:  
    build: .  
    environment:  
      MONGO_URL: "mongodb://db:27017/test"  
    depends_on:  
      - db  
    ports:  
      - "3000-3001:3000"  
    volumes:  
      - .:/usr/src  
      - /usr/src/node_modules  
    command: npx nodemon index.js  -- monitoreo  
    

  db:  
    image: mongo  

docker-compose build service_name1  
fig up -d  
alias fig  
env  
git status  

d o c k e r - c o m p o s e . o v e r r i d e . y m l - ports solo en uno de los dos  
version: "3.8"  

services:  
  app:  
    build: .  
  environment:  
    UNA_VARIABLE: "Hola Mundo"  

docker-compose up -d --scale my-app=2 - concurrente, balanceo de carga  

docker ps -q  
docker rm -f $(docker ps -aq) - para contenedores, buscar equivalente para imágenes  
docker network prune  
docker volume prune  
docker system prune  

docker run -d --name app --memory 1g image_name -- 4m OOMKilled  
docker stats  

docker stop looper  
docker ps -l  -- last process, status>128 excepción o señal no manejada correctamente  
docker kill looper  -- no opcion a gracefull shutdown sin afectar a los usuarios una vez que no tenga peticiones  
docker exec looper ps -ef  
CMD /loop.sh -- shell form como comando hijo del shell  
[CMD /loop.sh] -- exec form directo efecto al stop, no combinarlas  

docker run --name pinger ping hostname  
hostname  

d o c k e r f i l e  
FROM ubuntu:trusty  
ENTRYPOINT ["/bin/ping", "-c", "3"]  
CMD ["localhost"]  

docker run --name pinger ping  
docker run --name pinger ping google.com -- binarios autocontenidos que pueden correr en cualquier sistema de soporte docker sin necesidad de tenerlo instalado nativamente  

. d o c k e r i g n o r e -- contexto de build  

docker build -t prodapp -f build/production.Dockerfile . -- multi-stage build  

-- docker-in-docker, en linux todo es un archivo, montar socket en un contenedor con docker, puede ser inseguro  
docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock docker:19.03.12  
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/bin/docker wagoodman/dive:latest prodapp    
