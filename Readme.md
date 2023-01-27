## Service discovery consul

1. For up containers consul with docker:

```
docker compose up -d
```

2. For access sh of specific container. Exemplo consul01:

```
docker exec -it consul01 sh
```

3. Inside of container, run command bellow, to set the container with server in dev mode:

```
consul agent -dev
```

4. Inside of container, run command bellow, for view of the consul members:

```
consul members
```

5. If you want to view the nodes, you can use the Rest API, make a HTTP Request using the GET verb. For example: 

```
curl localhost:8500/v1/catalog/nodes
```
6. Inside of container, install the package bind-tools (to access the command dig):

```
apk -U add bind-tools
```

7. Now you can consul all nodes using DNS tools (dig)

```
dig @localhost -p 8600
dig @localhost -p 8600 consul01.node.consul
dig @localhost -p 8600 consul01.node.consul +short
```
