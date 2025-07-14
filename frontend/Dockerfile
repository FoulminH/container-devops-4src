## Base stage ##-----------------------------------
FROM node:current-bullseye-slim AS base
# set /app as working dir
WORKDIR /app

# Accept build-time env arg (default = prod)
ARG ENV=prod
ENV NODE_ENV=$ENV

# Copy package.json
COPY package.json ./

# install project dependencies
# Conditionally install dev deps
RUN npm install

## Build stage ##---------------------------------
FROM base AS builder

COPY . .

# Add build args to define VITE* vars
ARG VITE_PRODUCT_SERVICE_URL
ARG VITE_AUTH_SERVICE_URL
ARG VITE_ORDER_SERVICE_URL
# define VITE env vars for build
ENV VITE_PRODUCT_SERVICE_URL=${VITE_PRODUCT_SERVICE_URL}
ENV VITE_AUTH_SERVICE_URL=${VITE_AUTH_SERVICE_URL}
ENV VITE_ORDER_SERVICE_URL=${VITE_ORDER_SERVICE_URL}

RUN npm run build

## dev stage ##--------------------------------
FROM builder AS dev

# set NODE_ENV as dev
ENV NODE_ENV=development

EXPOSE 8080

CMD [ "npm", "run", "dev"]

## production stage ##--------------------------
## FIX ME: TRY TO RUN AS NGINX (reverse proxy for backend api ??)
FROM node:alpine3.22 AS prod

USER node

WORKDIR /app

COPY --chown=node:node --from=builder /app/package*.json ./
COPY --chown=node:node --from=builder /app/node_modules ./node_modules
COPY --chown=node:node --from=builder /app/dist ./dist
COPY --chown=node:node --from=builder /app/server.cjs ./server.cjs

ENV NODE_ENV=production

EXPOSE 8080
CMD ["npm", "run", "start"]
