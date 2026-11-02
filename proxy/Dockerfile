FROM nginx:alpine
RUN apk add --no-cache openssl
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/nginx.conf
RUN mkdir -p /etc/nginx/ssl
COPY /nginx/server.crt /etc/nginx/ssl/
COPY /nginx/server.key /etc/nginx/ssl/
EXPOSE 80 443
CMD ["nginx", "-g", "daemon off;"]
