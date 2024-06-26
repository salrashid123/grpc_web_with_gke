FROM node:latest as build-env-node

RUN apt-get update -y && apt-get install -y build-essential wget unzip curl

RUN curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v26.1/protoc-26.1-linux-x86_64.zip && \
    unzip protoc-26.1-linux-x86_64.zip -d protoc3 && \
    mv protoc3/bin/* /usr/local/bin/ && \
    mv protoc3/include/* /usr/local/include/

RUN wget  -O  /usr/local/bin/protoc-gen-grpc-web https://github.com/grpc/grpc-web/releases/download/1.5.0/protoc-gen-grpc-web-1.5.0-linux-x86_64
RUN chmod +x /usr/local/bin/protoc-gen-grpc-web

ADD . /app
WORKDIR /app
ADD package_build.json /app/package.json
RUN npm install -g protoc-gen-js
ENV NODE_OPTIONS=--openssl-legacy-provider
RUN mkdir www
RUN protoc -I=.  echo/echo.proto --js_out=import_style=commonjs:www --proto_path=. --grpc-web_out=import_style=commonjs,mode=grpcwebtext:www
RUN npm install --silent && \
    npx webpack client.js


ADD package_web.json /app/package.json
RUN npm  install --silent

EXPOSE  8000
CMD ["node", "app.js"]