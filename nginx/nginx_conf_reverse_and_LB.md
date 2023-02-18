## nginx reverse proxy

```
events {}

http {

  server {

    listen 8888;

    location / {

      return 200 "Hello from NGINX\n";

    }

    location /python {

      #add_header proxied  nginx;
      proxy_set_header proxied  nginx;
      proxy_pass 'http://localhost:8000/';

    }

    location /nginxorg {

      proxy_pass 'https://nginx.org/';

    }


  }
}
```