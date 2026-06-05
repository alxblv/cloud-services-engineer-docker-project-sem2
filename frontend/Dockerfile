# multistage building as described in https://v2.vuejs.org/v2/cookbook/dockerize-vuejs-app.htm
# using older version to avoid ERR_OSSL_EVP_UNSUPPORTED error
FROM node:26-alpine3.22 AS building-frontend-app

WORKDIR /frontend

# copy both 'package.json' and 'package-lock.json' (if available)
COPY package*.json ./

# install project dependencies
# npm ci instead of npm install - install exact dependencies versions directly from package-lock.json
RUN npm ci

# copy project files and folders to the current working directory
COPY . .

# build the app
ARG VUE_APP_API_URL=/api

RUN VUE_APP_API_URL=${VUE_APP_API_URL} npm run build

# deploy the application binary into a lean image
FROM nginx:1.30.2-alpine3.23 AS deploying-frontend-app

# curl will be used in HEALTHCHECK
RUN apk add --no-cache curl

# as we used npm run build, NODE_ENV is set to production
# and according to vue.config.js, the path includes momo-store
COPY --from=building-frontend-app /frontend/dist /usr/share/nginx/html/momo-store

# nginx needs config to proxy api to backend and to serve static stuff
# COPY nginx.conf /etc/nginx/conf.d/default.conf # MOUNTING IN docker-compose.yml INSTEAD!

# creating non-root user and changing owner (alpine)
RUN addgroup -S appuser && adduser -S appuser -G appuser
RUN chown -R appuser:appuser /usr/share/nginx/html /etc/nginx/conf.d /var/cache/nginx /run

# switching to a non-root user
USER appuser

EXPOSE 80

# without -f flag curl will return 0 even if HTTP status code is 4xx/5xx
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -f http://localhost/momo-store/

# daemon off - so the main process is active and container does not exit immediately
CMD ["nginx", "-g", "daemon off;"]