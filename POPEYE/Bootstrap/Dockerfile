FROM debian:bookworm-slim

WORKDIR /app

RUN apt-get update && apt-get upgrade -y && apt-get install -y nodejs npm

COPY src /app

RUN npm install

EXPOSE 3000

CMD ["node", "app.js"]
