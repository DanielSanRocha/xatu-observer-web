# Xatu Observer Web

Frontend to [xatu-observer](https://github.com/DanielSanRocha/xatu-observer). First download the dist folder in the releases page and copy it to your server. You can use Nginx to serve the static files and put the [server](https://github.com/DanielSanRocha/xatu-observer) under /api. For example:

```
server {
    server_name xatu.com;
    listen 80
    
    location /api/ {
        proxy_redirect off;
        proxy_pass http://localhost:8089/;
    }

    root /home/xatu/dist;
    
    index index.html index.htm index.nginx-debian.html;
    location / {
        try_files $uri $uri/ =404;
    }
}
```

## Build Setup

```bash
# install dependencies
$ yarn install

# serve with hot reload at localhost:3000
$ yarn dev

# build for production and launch server
$ yarn build
$ yarn start

# generate static project
$ yarn generate
```

Made with Love ❤️