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

