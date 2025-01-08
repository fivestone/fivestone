#### Documents
https://docs.rsshub.app/guide/

#### docker-compose.yml
```
version: '3.9'

services:
    rsshub:
        # two ways to enable puppeteer:
        # * comment out marked lines, then use this image instead: diygod/rsshub:chromium-bundled
        image: diygod/rsshub:2025-01-07
        # * (consumes more disk space and memory) leave everything unchanged
        container_name: fs_rsshub
        restart: always
        ports:
            - '1200:1200'
        environment:
            NODE_ENV: production
            CACHE_TYPE: redis
            REDIS_URL: 'redis://redis:6379/'
            PUPPETEER_WS_ENDPOINT: 'ws://browserless:3000'  # marked
            ALLOW_LOCALHOST: true # 允许所有本地访问
            ACCESS_KEY: password # 访问密钥，推荐开启。若要开启，将前面的#号去除即可，并按需定义
        depends_on:
            - redis
            - browserless  # marked

    browserless:  # marked
        image: browserless/chrome  # marked
        restart: always  # marked
        ulimits:  # marked
          core:  # marked
            hard: 0  # marked
            soft: 0  # marked

    redis:
        image: redis:alpine
        restart: always
        volumes:
            - redis-data:/data

volumes:
    redis-data:
```

#### Nginx
```
    location / # cannot deploy on sub folder?
        {
                proxy_pass http://127.0.0.1:1200;
                proxy_redirect off;
                proxy_set_header        Host    $host;
                proxy_set_header        X-Real-IP       $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        }
```

#### ACCESS_KEY
https://docs.rsshub.app/deploy/config#access-control-configurations

https://my.deploy/path?key=ACCESS_KEY
or
https://my.deploy/path?code=md5(path+ACCESS_KEY)

Python:
```
print(hashlib.md5(b'/path' + b'password').hexdigest())
```
