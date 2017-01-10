#FROM alpine:3.5
#FROM yikaus/alpine-bash
FROM yunai/alpine
MAINTAINER TENGINE Docker Maintainers "zhijiantianya@gmail.com"

# RUN apk update && apk upgrade

ENV TEGINE_VERSION 2.2.0

RUN apk add --no-cache --virtual .build-deps \
    gcc \
    curl \
    make \
    libc-dev \
    pcre-dev \
    openssl-dev

RUN curl -fSL http://tengine.taobao.org/download/tengine-$TEGINE_VERSION.tar.gz -o nginx.tar.gz

# TODO 增加用户和用户组 nginx
# TODO 日志输出流
# TODO COPY 配置
# 动态so
# html等默认文件

RUN \
 # 下载tengine TODO 判断是否md5正确 MD5: c5f040d07cdade7b618a4f04f1fdb8fa
    curl -fSL http://tengine.taobao.org/download/tengine-$TEGINE_VERSION.tar.gz -o nginx.tar.gz \
 # 解压tegine
 && mkdir -p /usr/src \
 && tar -zxC /usr/src -f nginx.tar.gz \
 && rm nginx.tar.gz \
 # 创建nginx用户和用户组
 && addgroup -S nginx \
 && adduser -D -S -h /var/cache/nginx -s /sbin/nologin -G nginx nginx \
 # 编译 (下面临时创建下）
 && CONFIG="\
        --prefix=/etc/nginx \
        --sbin-path=/usr/sbin/nginx \
        --conf-path=/etc/nginx/nginx.conf \
        --error-log-path=/var/log/nginx/error.log \
        --http-log-path=/var/log/nginx/access.log \
        --pid-path=/var/run/nginx.pid \
        --lock-path=/var/run/nginx.lock \
        --http-client-body-temp-path=/var/cache/nginx/client_temp \
        --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
        --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
        --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
        --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
        --user=nginx \
        --group=nginx \
    " \
 && cd /usr/src/tengine-$TEGINE_VERSION \
 && ./configure $CONFIG --with-debug \
 && make \
 && make install \
 && rm -rf /usr/src/tengine-$TEGINE_VERSION \
 # 移除apk
 && apk del .build-deps

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]