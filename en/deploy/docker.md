# 6.5 [Docker](https://www.docker.com/what-docker)

Docker is the worlds leading containerization platform. It automates the tasks of setting up and
configuring development and production environments. You can learn more about Docker at [docker.com](https://www.docker.com/).

This example assumes you are using a the following database configuration

_config/database.js_
```js
module.exports = {
  stores: {
    mongo: {
      adapter: require('sails-mongo'),
      host: process.env.MONGO_HOST ? process.env.MONGO_HOST : 'localhost',
      port: process.env.MONGO_PORT ? process.env.MONGO_PORT : 27017,
      database: process.env.MONGO_DATABASE ? process.env.MONGO_DATABASE : 'fabrix'
    }
  },

  models: {
    defaultStore: 'mongo',
    migrate: 'safe'
  }
};
```

### 1. Create a docker file in the root of your project

_Dockerfile_
```docker
#############################################
# Dockerfile to run fabrix-app/fabrix:latest
# Based on Alpine
#############################################

FROM node:7.8-alpine

MAINTAINER Jam Risser (jamrizzi)

ENV MONGO_HOST=db
ENV MONGO_PORT=27017
ENV MONGO_DATABASE=fabrix
ENV NODE_ENV=production

EXPOSE 3000

WORKDIR /app/

RUN apk add --no-cache \
        tini && \
    apk add --no-cache --virtual build-deps \
        git

COPY ./package.json /app/
RUN npm install
COPY ./ /app/
RUN apk del build-deps && \
    npm prune --production

ENTRYPOINT ["/sbin/tini", "--", "node", "/app/server.js"]
```

### 2. Add the following scripts to your `package.json`

_package.json_
```json
{
 "scripts": {
    "build": "docker build -t fabrix-app/fabrix:latest -f ./Dockerfile .",
    "push": "docker push fabrix-app/fabrix:latest",
    "run": "docker run --name some-fabrix --rm --link some-mongo:db -p 3000:3000 fabrix-app/fabrix:latest"
  }
}
```

### 3. Build your container

```sh
npm run build
```

### 4. Push your container

```sh
npm run push
```
