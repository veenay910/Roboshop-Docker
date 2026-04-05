FROM nginx:latest

# Remove default static content
RUN rm -rf /usr/share/nginx/html/*
RUN rm -rf /etc/nginx/nginx.conf

# Replace nginx config
COPY nginx.conf /etc/nginx/nginx.conf

# Copy frontend files
COPY static /usr/share/nginx/html/