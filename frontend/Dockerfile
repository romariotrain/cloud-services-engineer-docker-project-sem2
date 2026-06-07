FROM node:lts-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

ARG VUE_APP_API_URL=/api
ENV VUE_APP_API_URL=$VUE_APP_API_URL

COPY . .
RUN npm run build

FROM nginx:stable-alpine

COPY nginx.conf /etc/nginx/nginx.conf
COPY --from=builder /app/dist /usr/share/nginx/html/momo-store

RUN chown -R nginx:nginx /usr/share/nginx/html \
    && chown -R nginx:nginx /var/log/nginx \
    && touch /tmp/nginx.pid \
    && chown nginx:nginx /tmp/nginx.pid

USER nginx
EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD wget -qO- http://localhost:8080/momo-store/ || exit 1

CMD ["nginx", "-g", "daemon off;"]
