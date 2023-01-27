## nginx location block

```
$ cat /etc/nginx/nginx.conf
events {}

http {
  
  include mime.types;
 
  server {

    listen 80;
    server_name 34.125.158.244;
    
    root /sites/demo;

     # Preferential Prefix match  
    location ^~ /Greet2 {
      return 200 'Hello from NGINX "/greet" location.';
    }

#    # Exact match
#    location = /greet {
#      return 200 'Hello from NGINX "/greet" location.i - EXACT MATCH';
#    }
    
#    # REGEX match - case sensitive
#    location ~ /greet[0-9] { 
#      return 200 'Hello from NGINX "/greet" location. - REGEX MATCH';
#    }

    # REGEX match - case insensitive
    location ~* /greet[0-9] { 
      return 200 'Hello from NGINX "/greet" location. - REGEX MATCH INSENSITIVE';
    }
  }
}
```

## nginx variables

```
events {}

http {

  include mime.types;

  server {

    listen 80;
    server_name 34.125.158.244;

    root /sites/demo;

    set $weekend 'No';

    #Check if weekend
    if ( $date_local ~ 'Friday' ) {
      set $weekend 'Yes';
    }

    location /is_weekend {
      return 200 $weekend;
    }
#    #Check static API key
#    if ( $arg_apikey != 1234 ) {
#      return 401 "Incorrect API key";
#    }

#    location /inspect {
#
#      return 200 "Name: $arg_name";
#    }
  }
}
```

## nginx rewrites and redirects

```
events {}

http {

  include mime.types;

  server {

    listen 80;
    server_name 34.125.158.244;

    root /sites/demo;

    rewrite ^/user/(\w+) /greet/$1 last;
    rewrite /greet/evgeny /thumb.png;

    location /greet {

    return 200 "Hello User";
#    location /logo {
#
#     return 307 /thumb.png;
    }

    location = /greet/evgeny {
      return 200 "Hello Evgeny!";
    }
  }
}
```

## nginx try files and named locations

```
events {}

http {
  
  include mime.types;
 
  server {

    listen 80;
    server_name 34.125.158.244;
    
    root /sites/demo;


    try_files $uri /cat.png /greet @friendly_404;

    location @friendly_404 {

      return 404 "Sorry, that couldn not be found";
    }

    location /greet {

    return 200 "Hello User";
    }
  }
}
```

## nginx logging

```
events {}


http {
  
  include mime.types;
 
  server {

    listen 80;
    server_name 34.125.158.244;
    
    root /sites/demo;

    location /secure {

    access_log /var/log/nginx/secure.access.log;
    access_log /var/log/nginx/access.log;
#    access_log off;
    return 200 "Welcome to secure.";
    }
  }
}
```

## nginx index php fastcgi

```
user www-data;

events {}

http {
  
  include mime.types;
 
  server {

    listen 80;
    server_name 34.125.158.244;
    
    root /sites/demo;
  
    index index.php index.htmll;

    location / {
      try_files $uri $uri/ =404;
    }

    location ~\php$ {
      # Pass php requests to the php-fpm service (fastcgi)
      include fastcgi.conf;
      fastcgi_pass unix:/var/run/php.sock;
    }

  }
}
```

## nginx workers and pids

```
user www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {
  
  include mime.types;
 
  server {

    listen 80;
    server_name 34.125.158.244;
    
    root /sites/demo;
  
    index index.php index.htmll;

    location / {
      try_files $uri $uri/ =404;
    }

#    location ~\php$ {
#      # Pass php requests to the php-fpm service (fastcgi)
#      include fastcgi.conf;
#      fastcgi_pass unix:/var/run/php.sock;
#    }

  }
}
```

## Headers and Expires

```
user www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {

  include mime.types;

  server {

    listen 80;
    server_name 34.125.158.244;

    root /sites/demo;

    index index.php index.html;

    location / {
      try_files $uri $uri/ =404;
    }

#    location ~\php$ {
#      # Pass php requests to the php-fpm service (fastcgi)
#      include fastcgi.conf;
#      fastcgi_pass unix:/var/run/php.sock;
#    }

    location ~* \.(css|js|jpg|png)$ {
      access_log off;
#      add_header my_header "Hello world";
      add_header Cache-Control public;
      add_header Pragma public;
      add_header Vary Accept-Encoding;
      expires 1M;
    }
  }
}
```

## Compressed responses

`curl -I -H "Accept-Encoding: gzip, deflate" http://jw06/style.css`
HTTP/1.1 200 OK
Server: nginx/1.23.3
Date: Fri, 27 Jan 2023 21:53:56 GMT
Content-Type: text/css
Last-Modified: Wed, 21 Dec 2022 21:06:02 GMT
Connection: keep-alive
ETag: W/"63a3753a-3d5"
Expires: Sun, 26 Feb 2023 21:53:56 GMT
Cache-Control: max-age=2592000
Cache-Control: public
Pragma: public
Vary: Accept-Encoding
**Content-Encoding: gzip**


```
user www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {

  include mime.types;
  
  gzip on;
  gzip_comp_level 3;

  gzip_types text/css;
  gzip_types text/javascript;

  server {

    listen 80;
    server_name 34.125.158.244;

    root /sites/demo;

    index index.php index.html;

    location / {
      try_files $uri $uri/ =404;
    }

#    location ~\php$ {
#      # Pass php requests to the php-fpm service (fastcgi)
#      include fastcgi.conf;
#      fastcgi_pass unix:/var/run/php.sock;
#    }

    location ~* \.(css|js|jpg|png)$ {
      access_log off;
#      add_header my_header "Hello world";
      add_header Cache-Control public;
      add_header Pragma public;
      add_header Vary Accept-Encoding;
      expires 1M;
    }
  }
}
```