## HTTP load balancing

python3 -m http.server 10001
while sleep 1; do curl http://localhost:8888/server.txt; done


```
events {}

http {
  
  upstream http_servers {

#    ip_hash;
    least_conn;

    server localhost:10001;
    server localhost:10002;
    server localhost:10003;

  }

  server {
 
    listen 8888;
    
    location / {
      
      proxy_pass 'http://http_servers';
    }

  }

}
```