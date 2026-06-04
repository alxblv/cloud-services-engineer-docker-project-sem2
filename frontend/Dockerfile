# multistage building as described in https://v2.vuejs.org/v2/cookbook/dockerize-vuejs-app.htm
# using older version to avoid ERR_OSSL_EVP_UNSUPPORTED error
FROM node:16-alpine AS building-frontend-app

WORKDIR /frontend

# copy both 'package.json' and 'package-lock.json' (if available)
COPY package*.json ./

# install project dependencies
# npm ci instead of npm install - install exact dependencies versions directly from package-lock.json
RUN npm ci

# copy project files and folders to the current working directory
COPY . .

# build the app
RUN VUE_APP_API_URL=/api npm run build

# deploy the application binary into a lean image
FROM nginx:stable-alpine AS deploying-frontend-app

# as we used npm run build, NODE_ENV is set to production
# and according to vue.config.js, the path includes momo-store
COPY --from=building-frontend-app /frontend/dist /usr/share/nginx/html/momo-store

# nginx needs config to proxy api to backend and to serve static stuff
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

# daemon off - so the main process is active and container does not exit immediately
CMD ["nginx", "-g", "daemon off;"]