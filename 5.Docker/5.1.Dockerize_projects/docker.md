Stop​​ tất cả​​ containers:

```
docker ps -aq | xargs docker stop
```

Remove​​ tất cả​​ containers:

```
docker ps -aq | xargs docker rm
```

Remove​​ tất cả​​ images:

```
docker images -aq | xargs docker rmi
```

Remove​​ tất cả​​ networks:

```
docker network ls -q | xargs docker network rm
```

Remove​​ tất cả​​ volumes:

```
docker volume ls -q | xargs docker volume rm
```

Remove​​ tất cả​​ các​​ stopped containers, unused networks, dangling images,​​ và​​ build cache:

```
docker system prune
```