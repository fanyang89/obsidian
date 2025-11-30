路径：`/etc/nginx/nginx.conf`

```
location /syncthing/ {
    rewrite ^/syncthing/(.*)$ /$1 break;
    proxy_pass http://127.0.0.1:8384/;
}

location /adh/ {
    rewrite ^/adh/(.*)$ /$1 break;
    proxy_pass http://127.0.0.1:3001/;
    proxy_redirect / /adh/;
    proxy_cookie_path / /adh/;
}
```
