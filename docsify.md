
## framework

httpsdocsify.js.org#quickstart


## nginx config
server {
  listen 80;
  server_name www.zhoushen.xyz;
  location / {
      try_files $uri $uri/ /index.html;
      root /data/www/test/zhoushenn.github.io/docs;
      index  index.html index.htm;
      #add_header Cache-Control "no-cache, no-store";
  }
}

## webhook
https://github.com/adnanh/webhook  

start hook server  
nohup webhook -hooks hooks.json &  