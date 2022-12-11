# Create base node image and install runtime environment
FROM node:alpine AS base
RUN apk update && apk add curl bash && curl -sf https://gobinaries.com/tj/node-prune | sh

# Install the dependencies
FROM base AS builder
WORKDIR /build

# Copy workspace packages
COPY yarn.lock .
COPY package.json .
COPY app/package.json app/
COPY modules/eslint-config/package.json modules/eslint-config/
COPY modules/prettier-config/package.json modules/prettier-config/

# Install all dependencies
RUN yarn install --frozen-lockfile --non-interactive

# Build the sveltekit backend
COPY app app
COPY modules modules
ENV NODE_ENV=production
RUN yarn app build

# Clean node module folder for production
RUN npm prune --production --no-audit
RUN node-prune

# Copy relevant files from builder and start the server
FROM node:alpine
WORKDIR /frontend

RUN npm install pm2 -g

COPY --from=builder /build/app/package.json .
COPY --from=builder /build/node_modules ./node_modules/
COPY --from=builder /build/app/build ./build/

EXPOSE 3000
CMD [ "pm2-runtime", "-n", "app", "-i", "2", "build/index.js"]
