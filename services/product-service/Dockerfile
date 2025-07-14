# ------------ Base Stage: install dependencies ------------
FROM node:current-bullseye-slim AS base

WORKDIR /app

# Accept build-time env arg (default = prod)
ARG ENV=prod
ENV NODE_ENV=$ENV

# Copy package files
COPY package*.json ./

# Conditionally install dev deps
RUN if [ "$ENV" = "dev" ]; then \
      npm install; \
    else \
      npm install --omit=dev; \
    fi

## builder stage##---------------------------
FROM base AS builder

COPY . .

# ------------ Dev stage -------------------
FROM builder AS dev

RUN apt update && apt install libcurl4 -y

ARG ENV
COPY jest.config.js ./
COPY tests ./tests
COPY tests/jest.setup.js ./tests/jest.setup.js

EXPOSE 3000
CMD ["npm", "run", "dev"]


# ------------ Production stage ------------
FROM node:alpine3.22 AS prod

USER node

WORKDIR /app
# Copy only production files
COPY --chown=node:node --from=builder /app/package*.json ./
COPY --chown=node:node --from=builder /app/node_modules ./node_modules
COPY --chown=node:node --from=builder /app/src ./src

ENV NODE_ENV=production
EXPOSE 3000
CMD ["npm", "start"]
