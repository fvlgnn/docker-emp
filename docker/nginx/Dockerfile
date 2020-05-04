FROM nginx:1.17.8-alpine

RUN apk update && apk add --no-cache bash

CMD ["/bin/bash","-c","exec nginx -g 'daemon off;'"]

# use for environment in nginx config
# CMD ["/bin/bash","-c","envsubst '$$PROJECT_NAME' < /etc/nginx/conf.d/default.template.conf > /etc/nginx/conf.d/default.conf && exec nginx -g 'daemon off;'"]
