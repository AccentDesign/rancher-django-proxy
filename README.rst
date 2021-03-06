rancher-django-proxy
====================

An nginx proxy container for django

* The ``/media`` and ``/static`` volumes are ``/www/media`` and ``/www/static``
* Gzip is enabled
* The expected python container is assumed to be called ``app`` and on port ``8000``
* Http 1.1 is enabled so ``django-channels`` will work.

the default.conf file

.. code::

    server {

        listen 80;
        server_name localhost;
        charset utf-8;

        gzip on;
        gzip_proxied any;
        gzip_types
            text/css
            text/javascript
            text/xml
            text/plain
            application/javascript
            application/x-javascript
            application/json;

        location /favicon.ico {
            log_not_found off;
            access_log off;
        }

        location /media {
            alias /www/media;
            add_header Cache-Control must-revalidate;
        }

        location /static {
            alias /www/static;
            add_header Cache-Control must-revalidate;
        }

        location / {
            proxy_pass http://app:8000;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";

            proxy_redirect      off;
            proxy_set_header    Host $host;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header    X-Forwarded-Host $server_name;
        }

    }
