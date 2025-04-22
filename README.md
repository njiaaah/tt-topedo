# Topedo test task - todo app

## install and build

```sh
npm i
npm run build
```

## build docker image

```sh
docker build -t topedo .
```

## and run it

```sh
docker run -p 8080:80 topedo
```

## access via http://localhost:8080
