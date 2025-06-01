FROM node:20-alpine AS build

RUN apk add --no-cache bash curl

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

FROM node:20-alpine AS runtime

RUN apk add --no-cache bash curl sudo && \
    curl -1sLf 'https://dl.cloudsmith.io/public/infisical/infisical-cli/setup.alpine.sh' | bash && \
    apk update && \
    apk add infisical

ENV INFISICAL_API_URL=${INFISICAL_API_URL}
ENV INFISICAL_TOKEN=${INFISICAL_TOKEN}
ENV PROJECT_ID=${PROJECT_ID}

RUN addgroup -S nonroot && adduser -S nonroot -G nonroot

RUN echo 'nonroot ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers

WORKDIR /home/nonroot/app

COPY --from=build /app ./

RUN chown -R nonroot:nonroot ./

USER nonroot

EXPOSE 3000

ENTRYPOINT ["/bin/sh", "-c", "infisical run --token=$INFISICAL_TOKEN --domain=$INFISICAL_API_URL --projectId=$PROJECT_ID -- npm run dev"]