# angular-docker-app
Angular Docker Application

## In order to use this repo code as it is:

1. clone this repo
2. Follow only 4,5,6 steps mentioned below


## In order create new Angular docker application from scratch

Steps:

1. Create new angular app

    `ng new angular-docker-app`

2. create Dockerfile in the root folder of app
     ```
    # Step 1: Build the app in image 'builder'

    FROM node:10.4-alpine AS builder

    WORKDIR /usr/src/app
    COPY . .
    RUN npm ci && npm run build

    # Step 2: Use build output from 'builder'

    FROM nginx:stable-alpine
    LABEL version="1.0"

    COPY nginx.conf /etc/nginx/nginx.conf

    WORKDIR /usr/share/nginx/html
    COPY --from=builder /usr/src/app/dist/angular-docker-app/ . 
    
3. Create nginix.conf file 
    ```
    worker_processes  1;
    
    events {
        worker_connections  1024;
    }
    
    http {
        server {
            listen 80;
            server_name  localhost;
    
            root   /usr/share/nginx/html;
            index  index.html index.htm;
            include /etc/nginx/mime.types;
    
            gzip on;
            gzip_min_length 1000;
            gzip_proxied expired no-cache no-store private auth;
            gzip_types text/plain text/css application/json application/javascript application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    
            location / {
                try_files $uri $uri/ /index.html;
            }
        }
    }

4. Build the docker image

    ```docker image build -t angular-docker-app .```


5. Run the container 

    ```docker run -p 3000:80 --rm angular-docker-app```


6. Verify the output

    http://localhost:3000/



