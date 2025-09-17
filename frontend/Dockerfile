FROM node:22.19.0-alpine3.22 AS build
WORKDIR /app
COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm npm ci

COPY . .
ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build --configuration=production

FROM nginx:stable-alpine AS production

RUN mkdir -p /var/cache/nginx /var/run \
    && chown -R nginx:nginx /var/cache/nginx /var/run /var/log/nginx

COPY --chown=nginx:nginx --from=build /app/dist/frontend /usr/share/nginx/html
#COPY nginx.conf /etc/nginx/nginx.conf

# Удаляем дефолтный конфиг, чтобы монтировать свой через ConfigMap
RUN rm /etc/nginx/conf.d/default.conf

USER nginx
EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
