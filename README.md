# Docker Swarm Edu

## Dev cluster with docker on docker

**docker docker image**

https://hub.docker.com/\_/docker

**Create cluster**

```bash
docker-compose up -d \
&& docker-compose exec manager-1 docker swarm init \
&& token=$(docker-compose exec manager-1 docker swarm join-token manager -q | tr -cd "[:print:]") \
&& for((i=2;i<4;i++));do docker-compose exec manager-$i docker swarm join --token $token manager-1:2377;done \
&& token=$(docker-compose exec manager-1 docker swarm join-token worker -q | tr -cd "[:print:]") \
&& for((i=1;i<5;i++));do docker-compose exec worker-$i docker swarm join --token $token manager-1:2377;done \
&& for((i=1;i<3;i++));do docker-compose exec proxy-$i docker swarm join --token $token manager-1:2377;done
```

```bash
docker-compose exec manager-1 docker node update --label-add xyz.tgergo.function=proxy --label-add xyz.tgergo.zone=east proxy-1; \
docker-compose exec manager-1 docker node update --label-add xyz.tgergo.function=proxy --label-add xyz.tgergo.zone=west proxy-2; \
for((i=1;i<3;i++));do docker-compose exec manager-1 docker node update --label-add xyz.tgergo.zone=east worker-$i; done; \
for((i=3;i<5;i++));do docker-compose exec manager-1 docker node update --label-add xyz.tgergo.zone=west worker-$i; done
```

**Install packages**

```bash
for((i=1;i<4;i++));do docker-compose exec manager-$i apk add curl git; done; \
for((i=1;i<5;i++));do docker-compose exec worker-$i apk add curl; done
```

**Copy configurations**

```bash
docker cp deployment/ $(docker ps --filter name=manager-1 -q):/; \
docker-compose exec manager-1 docker config create -l porxy proxy-config deployment/nginx.conf
```

**Teardown**

```bash
docker-compose down && sudo rm -rf docker
```

**Images for experimentation/testing**

https://github.com/dockersamples

https://github.com/katacoda

https://hub.docker.com/r/benhall/dig

`docker run --name dig --network some-network benhall/dig dig service`
