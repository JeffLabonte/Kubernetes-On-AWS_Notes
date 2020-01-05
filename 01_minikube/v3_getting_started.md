FROM nginx:alpine

COPY files/index.html /usr/share/nginx/html/index.html
RUN apk add --no-cache bash
docker build -t hello:v3 -f Dockerfile_v3 .
