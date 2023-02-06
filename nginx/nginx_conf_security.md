## HTTPS

curl -Ik http://localhost
openssl dhparam 2048 -out /etc/nginx/ssl/dhparam.pem

```
user www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {

  include mime.types;

  # Redirect all traffic to HTTPS
  server {

    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
  }

  server {

    listen 443 ssl http2;
    server_name 34.125.158.244;

    root /sites/demo;

    index index.html;

    ssl_certificate /etc/nginx/ssl/self.crt;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    # Disable SSL
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # Optimise cipher suits
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

    # Enable DH Params
    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # Enable HSTS
    add_header Strict-Transport-Security "max-age=31536000" always;

    # SSL sessions
    ssl_session_cache shared:SSL:40m; # builtin
    ssl_session_timeout 4h;
    ssl_session_tickets on;

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

## Rate Limiting

siege -v -r 1 -c 6 https:/

```
ser www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {

  include mime.types;

  # Define limit zone
  limit_req_zone $request_uri zone=MYZONE:10m rate=60r/m; # =1r/s # $server_name or $binary_remote_addr

  # Redirect all traffic to HTTPS
  server {

    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
  }

  server {

    listen 443 ssl http2;
    server_name 34.125.158.244;

    root /sites/demo;

    index index.html;

    ssl_certificate /etc/nginx/ssl/self.crt;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    # Disable SSL
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # Optimise cipher suits
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

    # Enable DH Params
    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # Enable HSTS
    add_header Strict-Transport-Security "max-age=31536000" always;

    # SSL sessions
    ssl_session_cache shared:SSL:40m; # builtin
        ssl_session_cache shared:SSL:40m; # builtin
        ssl_session_timeout 4h;
        ssl_session_tickets on;

        location / {
          limit_req zone=MYZONE burst=5 nodelay;
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

## Basic Auth

htpasswd -c /etc/nginx/.htpasswd user1

```
ser www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {

  include mime.types;

  # Define limit zone
  limit_req_zone $request_uri zone=MYZONE:10m rate=60r/m; # =1r/s # $server_name or $binary_remote_addr

  # Redirect all traffic to HTTPS
  server {

    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
  }

  server {

    listen 443 ssl http2;
    server_name 34.125.158.244;

    root /sites/demo;

    index index.html;

    ssl_certificate /etc/nginx/ssl/self.crt;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    # Disable SSL
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # Optimise cipher suits
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

    # Enable DH Params
    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # Enable HSTS
    add_header Strict-Transport-Security "max-age=31536000" always;

    # SSL sessions
    ssl_session_cache shared:SSL:40m; # builtin
        ssl_session_cache shared:SSL:40m; # builtin
        ssl_session_timeout 4h;
        ssl_session_tickets on;

        location / {
          auth_basic "Secure Area";
          auth_basic_user_file /etc/nginx/.htpasswd;
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

## Hardening

curl -Ik
check nginx version

```
ser www-data;

worker_processes auto;

pid /var/run/new_nginx.pid;

events {
  worker_connections 512;
}

http {

  include mime.types;

  server_tokens off; # don't show nginx version

  # Redirect all traffic to HTTPS
  server {

    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
  }

  server {

    listen 443 ssl http2;
    server_name 34.125.158.244;

    root /sites/demo;

    index index.html;

    add_header X-Frame-Options "SAMEORIGIN" # Prevents content redirection from linked site

    ssl_certificate /etc/nginx/ssl/self.crt;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    # Disable SSL
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # Optimise cipher suits
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+AES256:ECDH+AES128:DH+3DES:!ADH:!AECDH:!MD5;

    # Enable DH Params
    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # Enable HSTS
    add_header Strict-Transport-Security "max-age=31536000" always;

    # SSL sessions
    ssl_session_cache shared:SSL:40m; # builtin
        ssl_session_cache shared:SSL:40m; # builtin
        ssl_session_timeout 4h;
        ssl_session_tickets on;

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
