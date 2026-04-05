FROM ngnix
RUN rm -rf /usr/share/nginx/html/* 
RUN rm -rf /etc/nginx/nginx.conf
COPY ngnix.conf /etc/nginx/nginx.conf
COPY static /usr/share/nginx/html/