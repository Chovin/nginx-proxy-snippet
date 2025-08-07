# General Nginx Proxy Config

Set this up alongside any containers you want to reverse proxy / add letsencrypt to

run with `docker compose up -d`.
might need to `docker network remove proxy-network` if it was orphaned from prior runs

example containers you might run behind this

`docker-compose.yml`
```yaml
services:
    frontend:
        image: something
        environment:
            # the url you will be accessing it at
            VIRTUAL_HOST: blah.com
            # the port that this container is listening on that nginx should forward to 80/443
            VIRTUAL_PORT: 8080
            # letsencrypt needs this to know what url to create the ssl cert for
            LETSENCRYPT_HOST: blah.com
    
    backend:
        image: something_else
        environment:
            VIRTUAL_HOST: blah.com
            VIRTUAL_PORT: 80
            # specifies what path it will be accessed on, ie. blah.com/api
            VIRTUAL_PATH: /api
    
    pma:
        image: phpmyadmin:latest
        environment:
            # you can do subdomains as well
            VIRTUAL_HOST: pma.blah.com
            VIRTUAL_PORT: 80
            # just remember to setup letsencrypt as well
            LESTENCRYPT_HOST: pma.blah.com
```

# FAQ

1. getting `Error response from daemon: error while creating mount source path '.../certs': mkdir .../certs: permission denied`:
   check your folder permissions, or it might be SELinux. If it's SELinux, change `/etc/selinux/config` to `permissive`, restart the server `sudo shutdown -r now`, `docker compose up -d`, set it back to `enforced`, restart again `sudo shutdown -r now`, and finally see if it works: `docker compose up -d`
2. custom certs not working:
   It may be file permissions, they should be owned by you in rootless mode