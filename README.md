# microservices - quick guide  

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

d o c k e r    f i l e  
FROM ubuntu:latest  
RUN touch /usr/src/hola-platzi.txt  -- misma capa

docker build -t ubuntu:name .  
docker run --rm -p 6006:6006 name_of_image  
docker login  
docker tag ubuntu:name user_name/ubuntu:platzi  
docker push user_name/ubuntu:platzi  

docker history ubuntu:name  
https://github.com/wagoodman/dive  
dive ubuntu:name  
https://docs.docker.com/engine/reference/commandline/commit/  

-- desarrollo  

https://github.com/cris-her/docker  


docker-compose build   
docker-compose up  
docker-compose stop service_name  

