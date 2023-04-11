###################
# BUILD FOR LOCAL DEVELOPMENT
###################

FROM node:18-alpine as dev

WORKDIR /app

COPY --chown=node:node ./package*.json /app

RUN npm ci

COPY --chown=node:node . .

USER node

###################
# BUILD FOR PRODUCTION
###################

FROM node:18-alpine as build

WORKDIR /app

COPY --chown=node:node --from=dev ./node_modules /app/node_modules
COPY --chown=node:node ./package*.json /app
COPY --chown=node:node ./tsconfig.json /app
COPY --chown=node:node . .

RUN npm run build

ENV NODE_ENV production

RUN npm ci --only=production && npm cache clean --force

USER node

###################
# PRODUCTION
###################

FROM node:18-alpine as prod

COPY --chown=node:node --from=build ./node_modules /app/node_modules
COPY --chown=node:node --from=build ./dist /app/dist

CMD [ "node", "dist/main.js" ]