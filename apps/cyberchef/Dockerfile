FROM node:18-alpine AS builder

ARG VERSION

WORKDIR /app

RUN apk add git \
 && git clone --depth=1 https://github.com/gchq/CyberChef.git .

# Install dependencies
# --ignore-scripts prevents postinstall script (which runs grunt) as it depends on files other than package.json
RUN npm ci --ignore-scripts

# Copy files needed for postinstall and build
# COPY . .

# npm postinstall runs grunt, which depends on files other than package.json
RUN npm run postinstall

# Build the app
RUN npm run build


FROM nginxinc/nginx-unprivileged:alpine AS app

COPY --from=builder /app/build/prod /usr/share/nginx/html/
