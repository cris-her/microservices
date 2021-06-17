# microservices - quick guide


docker run --name repo alpine/git clone \  
  https://github.com/docker/getting-started.git  
docker cp repo:/git/getting-started/ .  


cd getting-started  
docker build -t docker101tutorial .  


docker run -d -p 80:80 \
  --name docker-tutorial docker101tutorial  


docker ps  
docker ps -a  
docker image ls  
docker build -t name_of_image .  
docker run --rm -p 6006:6006 name_of_image  


docker-compose up  
docker-compose stop service_name  

