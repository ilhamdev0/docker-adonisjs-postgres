ARG NODE_IMAGE=node:18-alpine

#First Stage
FROM $NODE_IMAGE AS base
RUN apk --no-cache add dumb-init
RUN mkdir -p /home/node/app && chown node:node /home/node/app
WORKDIR /home/node/app
USER node
RUN mkdir tmp

#Local Development Stage
FROM base AS dev

COPY --chown=node:node source/package*.json ./
RUN ["npm", "ci"]
COPY --chown=node:node source/ ./
CMD ["node", "ace", "serve", "--watch"]
EXPOSE 3333

#Production Stage
FROM base AS prod

ENV NODE_ENV=production

COPY --chown=node:node source/package*.json ./
RUN npm ci --only=production && npm cache clean --force
COPY --chown=node:node source/ ./
RUN ["node", "ace", "build", "--production"]
CMD ["dumb-init", "node", "server.js"]
EXPOSE 3333