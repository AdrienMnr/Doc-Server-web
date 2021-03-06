# Vhost Nginx pour Symfony 4
```bash
server {
    listen 80;
    server_name WWW.NAME_SITE.COM;
    rewrite ^/(.*) http://NAME_SITE.COM/$1 permanent;
}

server {
    listen 80;
    server_name NAME_SITE.COM;
    root /var/www/NAME_SITE/web;
    
    add_header Strict-Transport-Security max-age=15768000;
    add_header X-Frame-Options SAMEORIGIN;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
   
    location / {
        # try to serve file directly, fallback to app.php
        try_files $uri /app.php$is_args$args;
    }
    # PROD
    location ~ ^/app\.php(/|$) {
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        include fastcgi_params;
        # When you are using symlinks to link the document root to the
        # current version of your application, you should pass the real
        # application path instead of the path to the symlink to PHP
        # FPM.
        # Otherwise, PHP's OPcache may not properly detect changes to
        # your PHP files (see https://github.com/zendtech/ZendOptimizerPlus/issues/126
        # for more information).
        fastcgi_param PHP_VALUE "session.cookie_secure=1;\n session.cookie_httponly=1;";
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        fastcgi_param DOCUMENT_ROOT $realpath_root;
        # Prevents URIs that include the front controller. This will 404:
        # http://domain.tld/app.php/some-path
        # Remove the internal directive to allow URIs like this
        internal;
    }
    # return 404 for all other php files not matching the front controller
    # this prevents access to other php files you don't want to be accessible.
    location ~ \.php$ {
      return 404;
    }
    
    error_log /var/log/NAME_SITE_error.log;
    access_log /var/log/NAME_SITE_access.log;
}
```
