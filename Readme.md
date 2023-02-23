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

7. Now you can consul all nodes using DNS tools (dig):

```
dig @localhost -p 8600
dig @localhost -p 8600 consul01.node.consul
dig @localhost -p 8600 consul01.node.consul +short
```

8. View consul members:

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

12. Join consul servers:

```
docker exec -it consulserver03 sh
consul join 172.21.0.3 //ip consulserver02
exit
docker exec -it consulserver01 sh
consul join 172.21.0.3 //ip consulserver02
```

13. Configuring first consul client:

```
docker exec -it consulclient01 sh
mkdir /var/lib/consul
ifconfig //get ip
consul agent -bind=172.21.0.5 -data-dir=/var/lib/consul -config-dir=/etc/consul.d
```

14. Join client in the cluster:

```
docker exec -it consulclient01 sh
consul members
consul join 172.21.0.4
```

15. Reload services:

```
docker exec -it consulclient01 sh
consul reload
```

16. Searching the services registry using DNS tools (dig):

```
apk -U add bind-tools
dig @localhost -p 8600 SRV
dig @localhost -p 8600 nginx.service.consul
```

17. Searching the services using the API of consul:

```
docker exec -it consulserver01 sh
curl localhost:8500/v1/catalog/services
```

18. Searching the services using the CLI of consul:

```
docker exec -it consulserver01 sh
consul catalog nodes -service nginx
consul catalog nodes -detailed
```

19. Registry consul client and join on the cluster with flag -retry-join:

```
docker exec -it consulclient02 sh
mkdir /var/lib/consul
ifconfig //getip
consul agent -bind=172.21.0.6 -data-dir=/var/lib/consul -config-dir=/etc/consul.d -retry-join=172.21.0.2 -retry-join=172.21.0.3 -retry-join=172.21.0.4
```

20. Viewing the consul members:

```
docker exec -it consulclient02 sh
consul members
```

21. Viewing all clients of nginx service:

```
docker exec -it consulclient02 sh
apk -U add bind-tools
dig @localhost -p 8600 nginx.service.consul
```

22. Installing and testing nginx in consul client 01:

```
docker exec -it consulclient01 sh
apk -U add nginx
nginx
ps
curl localhost
```


23. Altering the root page of nginx

```
docker exec -it consulclient01 sh
apk -U add vim
mkdir /usr/share/nginx/html -p
vim /etc/nginx/http.d/default.conf
Erase this block:
# Everuthing is a 404
location / {
    return 404;
}

Add this:
root /usr/share/nginx/html

```

24. Creating the index.html default of nginx

```
vim /usr/share/nginx/html/index.html
Put this content inside of file:
Hello
```


25. Restarting nginx server

```
nginx -s reload 
curl localhost
dig @localhost -p 8600 nginx.service.consul
```

26. Running consul in servers

```
docker exec -it consulserver01 sh
consul agent -config-dir=/etc/consul.d

docker exec -it consulserver02 sh
consul agent -config-dir=/etc/consul.d

docker exec -it consulserver03 sh
consul agent -config-dir=/etc/consul.d
```

27. Generate encrypt key for consul server

```
consul keygen
```

28. Installing TCP dump tool

```
apk add tcpdump
```

29. View specific port in tcpdump 

```
tcpdump -i eth0 -an port 8301 -A
```

30. Set user interface consul

```
docker exec -it consulserver03 sh
consul agent -config-dir=/etc/consul.d - ui
```