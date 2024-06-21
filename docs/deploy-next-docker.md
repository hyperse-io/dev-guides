# Deploy next.js docker guides

### Dockerfile

```shell
FROM node:18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
# RUN apk add --no-cache libc6-compat --repository https://mirrors.aliyun.com/ubuntu/
RUN apk add --no-cache libc6-compat

ENV LC_ALL zh_CN.UTF-8

WORKDIR /opt

RUN npm config set registry  https://registry.npmmirror.com

# Install dependencies based on the preferred package manager
COPY package.json yarn.lock* .yarnrc.yml package-lock.json* pnpm-lock.yaml* ./
COPY .yarn/releases/ ./.yarn/releases/

RUN \
  if [ -f yarn.lock ]; then yarn --immutable; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then yarn global add pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi


# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /opt
COPY --from=deps /opt/node_modules ./node_modules
COPY . .

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
# ENV NEXT_TELEMETRY_DISABLED 1
# RUN yarn generate

RUN yarn build-docker
# If using npm comment out above and use below instead
# RUN npm run build

# Production image, copy all the files and run next
FROM base AS runner
RUN apk add --no-cache curl
WORKDIR /opt

ENV NODE_ENV production
# Uncomment the following line in case you want to disable telemetry during runtime.
# ENV NEXT_TELEMETRY_DISABLED 1

# RUN addgroup --system --gid 1001 nodejs
# RUN adduser --system --uid 1001 nextjs
RUN addgroup --system --gid 700 op
RUN adduser --system --uid 700 op

# COPY --from=builder /opt/public ./public

# Set the correct permission for prerender cache
RUN mkdir .next
RUN chown op:op .next

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=op:op /opt/.next/standalone ./
COPY --from=builder --chown=op:op /opt/.next/static ./.next/static

USER op

# 无需修改
EXPOSE 8080

ENV PORT 8080
# set hostname to localhost
ENV HOSTNAME "0.0.0.0"
COPY start.sh /opt/start.sh
USER root
RUN chmod +x /opt/start.sh
USER op
# server.js is created by next build from the standalone output
# https://nextjs.org/docs/pages/api-reference/next-config-js/output
CMD sh start.sh
```

### packages.json scripts

```json
"scripts": {
    "dev": "APP_ENV=rc hyper-env -- next dev",
    "build": "next build",
    "build-docker": "cross-env NEXT_BUILD_ENV_OUTPUT=standalone next build && hyper-next-standalone",
    "g:changeset": "changeset",
    "g:cz": "cz",
    "g:fix-all-files": "eslint . --ext .ts,.tsx,.js,.jsx,.cjs,.mjs,.mdx,.graphql --fix",
    "g:lint-staged-files": "lint-staged --allow-empty",
    "g:version": "changeset version",
    "postinstall": "is-ci || yarn husky install",
    "localbuild": "next build",
    "start": "APP_ENV=prod hyper-env -- next start",
    "start:integration": "APP_ENV=integration hyper-env -- next start",
    "start:prod": "APP_ENV=prod hyper-env -- next start",
    "start:rc": "APP_ENV=rc hyper-env -- next start",
    "docker": "APP_ENV=prod ./node_modules/@hyperse/hyper-env/bin/hyper-env.mjs -- node server.js",
    "docker:integration": "APP_ENV=integration ./node_modules/@hyperse/hyper-env/bin/hyper-env.mjs -- node server.js",
    "docker:prod": "APP_ENV=prod ./node_modules/@hyperse/hyper-env/bin/hyper-env.mjs -- node server.js",
    "docker:rc": "APP_ENV=rc ./node_modules/@hyperse/hyper-env/bin/hyper-env.mjs -- node server.js"
  },
```

### start.sh

```shell
# sleep 100000
exec npm run docker:${DeployEnv}

```

### Next runtime env

packages.json

```json
"next-runtime-env": "^3.2.2",
```

### The examples of docker files

- [development docker](docker/development) _(macOS & Linux)_
- [production docker](docker/production) _(macOS & Linux)_
- [staging docker](docker/staging) _(macOS & Linux)_
