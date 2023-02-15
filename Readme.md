## Service discovery consul

1. For up containers consul with docker:

```
docker compose up -d
```

2. For access sh of specific container. Exemplo consulserver01:

```
docker exec -it consulserver01 sh
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

8. View consul members

```
consul members
```

9. Subscriber de consulserver 01 with member:

```
docker exec -it consulserver01 sh
ifconfig //get ip 
mkdir /etc/consul.d
mkdir /var/lib/consul
consul agent -server -bootstrap-expect=3 -node=consulserver01 -bind=172.21.0.4 -data-dir=/var/lib/consul -config-dir=/etc/consul.d
```

10. Subscriber de consulserver02 with member:

```
docker exec -it consulserver02 sh
ifconfig //get ip 
mkdir /etc/consul.d
mkdir /var/lib/consul
consul agent -server -bootstrap-expect=3 -node=consulserver02 -bind=172.21.0.3 -data-dir=/var/lib/consul -config-dir=/etc/consul.d
```


11. Subscriber de consulserver03 with member:

```
docker exec -it consulserver03 sh
ifconfig //get ip 
mkdir /etc/consul.d
mkdir /var/lib/consul
consul agent -server -bootstrap-expect=3 -node=consulserver03 -bind=172.21.0.2 -data-dir=/var/lib/consul -config-dir=/etc/consul.d
```

12. Join consul servers

```
docker exec -it consulserver03 sh
consul join 172.21.0.3 //ip consulserver02
exit
docker exec -it consulserver01 sh
consul join 172.21.0.3 //ip consulserver02
```