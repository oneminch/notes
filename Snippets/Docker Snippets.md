## Remove All Stopped Containers

```bash
docker container prune -f
```

## Remove All Containers

```bash
docker rm $(docker ps -aq)
```

## Stop All Running Containers

```bash
docker stop $(docker ps -q)
```
