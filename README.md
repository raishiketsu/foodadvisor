# FoodAdvisor - Strapi Demo

オリジナル
https://github.com/strapi/foodadvisor

git clone https://github.com/strapi/foodadvisor.git

add Dockerfile

バックエンド strapi

```
FROM node:16

WORKDIR /app
ADD ./package.json /app
ADD ./yarn.lock /app

RUN yarn install --frozen-lockfile

ADD . /app

RUN yarn seed

EXPOSE 1337

CMD ["yarn", "develop"]

```
フロンドエンド　Next.js
```
FROM node:16

WORKDIR /app
ADD ./package.json /app
ADD ./yarn.lock /app
ADD ./.env.development /app

RUN yarn install --frozen-lockfile

ADD . /app

EXPOSE 3000

CMD ["yarn", "dev"]

```

github actions
```
name: Docker Image CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

permissions:
  contents: read
  packages: write

jobs:
  build_and_push_api:
    runs-on: ubuntu-latest
    env:
      IMAGE_NAME: foodadvisor-api
    steps:
      - name: checkout
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v1
        with:
          registry: ghcr.io
          username: ${{github.actor}}
          password: ${{secrets.GITHUB_TOKEN}}

      - name: Build and push
        uses: docker/build-push-action@v2
        with:
          context: ./api
          push: true
          tags: |
            ghcr.io/${{ github.repository_owner }}/${{ env.IMAGE_NAME }}:latest
            
  build_and_push_client:
    runs-on: ubuntu-latest
    env:
      IMAGE_NAME: foodadvisor-client
    steps:
      - name: checkout
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v1
        with:
          registry: ghcr.io
          username: ${{github.actor}}
          password: ${{secrets.GITHUB_TOKEN}}

      - name: Build and push
        uses: docker/build-push-action@v2
        with:
          context: ./client
          push: true
          tags: |
            ghcr.io/${{ github.repository_owner }}/${{ env.IMAGE_NAME }}:latest
```