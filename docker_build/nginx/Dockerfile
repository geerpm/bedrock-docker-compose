# docker/nginx/Dockerfile
FROM nginx:alpine

ADD ./docker_build/nginx/nginx.conf /etc/nginx/conf.d/default.conf

# workspace
ARG APP_DIR=/app/bedrock
ADD ./bedrock $APP_DIR

## debug
# RUN apk add nano openrc
