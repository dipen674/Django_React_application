FROM node:16-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm install --production=false
COPY . .
RUN npm run build 

FROM nginx:alpine AS production

RUN touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid

RUN chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    chown -R nginx:nginx /etc/nginx/conf.d

COPY --from=builder --chown=nginx:nginx /app/build /usr/share/nginx/html
COPY --chown=nginx:nginx ./nginx.conf /etc/nginx/conf.d/devconnector.conf

RUN rm /etc/nginx/conf.d/default.conf

USER nginx

EXPOSE 8080


CMD ["nginx", "-g", "daemon off;"]