# web-home dockerlize

try to release `home` and `upimage` by multiple costomed `docker images`.

## web-server

expose `home`, `statics` and `mirrors` via nginx

```Dockerfile
FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
COPY static-html-directory /usr/share/nginx/html
```

## upimage

deploy `upimage` by `docker`. It's not necessary to build `native app` anymore.

## simple dockerfile reference

### dokerfile format

1. a `dockerfile` must begin with a `FROM` instruction.
2. `COPY`
