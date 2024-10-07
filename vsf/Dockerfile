ARG IMAGE_NODE_VERSION="20.10.0"
ARG YARN_VERSION="3.6.1"


FROM node:${IMAGE_NODE_VERSION}-slim AS vsf-pre

ARG TAG_BRANCH="main" \
    NUXT_STORAGE_DRIVER="redis" \
    NUXT_STORAGE_URL="redis://redis:6379"

ENV NUXT_STORAGE_URL=$NUXT_STORAGE_URL \
    NUXT_STORAGE_DRIVER=$NUXT_STORAGE_DRIVER \
    TAG_BRANCH=$TAG_BRANCH \
    NODE_VERSION=$IMAGE_NODE_VERSION
    
WORKDIR /src

RUN apt-get update &&  apt-get install -y --no-install-recommends git ca-certificates \
  && rm -rf /var/lib/apt/lists/* \
  && git clone --depth=1 --recurse-submodules --branch ${TAG_BRANCH} https://github.com/odoogap/storefront-ui . \
  && yarn add wait-on -W \
  && yarn install \
  --prefer-offline \
  --frozen-lockfile \
  --non-interactive \
  --production=false \
  && yarn build

FROM vsf-pre AS vsf2

ARG IMAGE_NODE_VERSION

ENV PORT=$PORT \
    NUXT_STORAGE_URL=$NUXT_STORAGE_URL \
    HOST=0.0.0.0 \
    NODE_VERSION=$IMAGE_NODE_VERSION

COPY --from=vsf-pre /src/.output ./
RUN echo "___::: NUXT_STORAGE_URL=$NUXT_STORAGE_URL , NUXT_STORAGE_DRIVER=$NUXT_STORAGE_DRIVER, NODE_VERSION=$NODE_VERSION"

CMD [ "node", "/src/server/index.mjs" ]
