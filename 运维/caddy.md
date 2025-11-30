#caddy #http-server #dns #certbot

```
{
          admin "unix//run/caddy/admin.socket"
  }

  (dufs) {
          reverse_proxy http://127.0.0.1:5000
  }

  (uofs) {
          root * /mnt/uofs2
          file_server browse {
                  hide .Trash-501 .trash .config .accesslog .stats .kopiaignore
          }
  }

  :80/f, :80/f/* {
          import dufs
  }

  :80 {
          import uofs
  }

  ds.fuis.me/f, ds.fuis.me/f/* {
          import dufs
  }

  ds.fuis.me {
          tls /etc/letsencrypt/live/ds.fuis.me/fullchain.pem /etc/letsencrypt/live/ds.fuis.me/privkey.pem
          import uofs
  }

  import /etc/caddy/conf.d/*
```

```bash
#!/usr/bin/env bash

sudo certbot certonly --agree-tos --email 'fanyang89@outlook.com' --manual --preferred-challenges dns --domains memos.fuis.me
sudo chown -R caddy:caddy /etc/letsencrypt

# Certificate is saved at: /etc/letsencrypt/live/ds.fuis.me/fullchain.pem
# Key is saved at:         /etc/letsencrypt/live/ds.fuis.me/privkey.pem
# This certificate expires on 2024-08-21.
```

```
Certificate is saved at: /etc/letsencrypt/live/memos.fuis.me/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/memos.fuis.me/privkey.pem
This certificate expires on 2024-08-21.
```

## 续签

```bash
# arc
sudo certbot certonly --manual --preferred-challenges dns -d 'jenkins.fuis.me'
sudo certbot certonly --manual --preferred-challenges dns -d 'search.fuis.me'
# www
sudo certbot certonly --manual --preferred-challenges dns -d 'ds.fuis.me'
sudo certbot certonly --manual --preferred-challenges dns -d 'git.fuis.me'
sudo certbot certonly --manual --preferred-challenges dns -d 'memos.fuis.me'
sudo certbot certonly --manual --preferred-challenges dns -d 'up.fuis.me'
```
