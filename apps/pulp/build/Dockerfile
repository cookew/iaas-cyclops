FROM node:24-alpine

ARG VERSION=v0.1.24

RUN apk add --no-cache git \
    && git clone -b "${VERSION}" --depth 1 https://github.com/pulp/pulp-ui.git /pulp \
    && cd /pulp \
    && npm install \
    && rm -rf \
        /root/.cache \
        /root/.npm \
        /usr/local/share \
        /var/cache/apk/* \
        /var/log/*

ENV PATH /pulp/node_modules/.bin:$PATH
EXPOSE 8002
WORKDIR /pulp/app
CMD ["npm", "run", "start"]
