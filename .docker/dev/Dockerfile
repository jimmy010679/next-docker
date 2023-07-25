ARG NODE_VERSION

FROM node:${NODE_VERSION}-alpine

RUN apk update && apk add bash

WORKDIR /cache
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN \
    if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
    elif [ -f package-lock.json ]; then npm ci; \
    elif [ -f pnpm-lock.yaml ]; then yarn global add pnpm && pnpm i; \
    else echo "Lockfile not found." && exit 1; \
    fi
RUN yarn

WORKDIR /app
# ENV PATH /app/node_modules/.bin:$PATH
COPY . .