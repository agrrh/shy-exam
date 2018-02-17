FROM node:8-alpine

RUN apk update && apk add bash
WORKDIR /app
COPY ./main.js /app/main.js
CMD ["node", "/app/main.js"]
