# Docker Swarm Edu

## Dev cluster with docker on docker

**docker docker image**

https://hub.docker.com/\_/docker

**Create cluster**

```
docker-compose up -d \
&& docker-compose exec manager-1 docker swarm init \
&& token=$(docker-compose exec manager-1 docker swarm join-token manager -q | tr -cd "[:print:]") \
&& for((i=2;i<4;i++));do docker-compose exec manager-$i docker swarm join --token $token manager-1:2377;done \
&& token=$(docker-compose exec manager-1 docker swarm join-token worker -q | tr -cd "[:print:]") \
&& for((i=1;i<5;i++));do docker-compose exec worker-$i docker swarm join --token $token manager-1:2377;done \
&& docker-compose exec rev-proxy docker swarm join --token $token manager-1:2377
```

`docker-compose down && sudo rm -rf docker`
