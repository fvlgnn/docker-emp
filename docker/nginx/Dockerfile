FROM nginx:1.14.2-alpine

RUN apk update && apk add --no-cache bash

CMD ["/bin/bash","-c","envsubst '$$HTTP_PORT_EXPOSED $$HTTPS_PORT_EXPOSED $$SERVER_HOST_NAME' < /etc/nginx/conf.d/default.template > /etc/nginx/conf.d/default.conf && exec nginx -g 'daemon off;'"]
