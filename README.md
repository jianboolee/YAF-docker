# YAF-docker
最小PHP YAF的docker镜像
[![](https://images.microbadger.com/badges/version/newfuture/yaf.svg)](https://hub.docker.com/r/newfuture/yaf/) [![](https://images.microbadger.com/badges/image/newfuture/yaf.svg)](https://microbadger.com/images/newfuture/yaf "datails")

`newfuture/yaf` : the minimal docker image for yaf extension (default environment is dev )

## Details 

PHP YAF images based on alpine (the mini docker image which is about 1.8MB after compressed !)


### all images:

* [![](https://images.microbadger.com/badges/image/newfuture/yaf:php7.svg)](https://microbadger.com/images/newfuture/yaf:php7) [`php7` (latest)](https://github.com/NewFuture/YAF-docker/tree/docker/php5/cli/)
* [![](https://images.microbadger.com/badges/image/newfuture/yaf:fpm-php7.svg)](https://microbadger.com/images/newfuture/yaf:fpm-php7) [`fpm-php7`](https://github.com/NewFuture/YAF-docker/blob/docker/php7/fpm/)
* [![](https://images.microbadger.com/badges/image/newfuture/yaf:php5.svg)](https://microbadger.com/images/newfuture/yaf:php5) [`php5`](https://github.com/NewFuture/YAF-docker/tree/docker/php5/cli/)
* [![](https://images.microbadger.com/badges/image/newfuture/yaf:fpm-php5.svg)](https://microbadger.com/images/newfuture/yaf:fpm-php5) [`fpm-php5`](https://github.com/NewFuture/YAF-docker/blob/docker/php7/fpm/)

### include the latest php extensions:
- [YAF](https://github.com/laruence/yaf)
- [php-memcached](https://pecl.php.net/package/memcached)
- [php-redis](https://pecl.php.net/package/redis)
- PDO-*
- mcrypt
- curl
- gd

## Environment var

| VAR | default | description |
| --- | --- | --- |
| `TIMEZONE` | UTC | for `date.timezone` setting ini php.ini |
|`MAX_UPLOAD` | 50M | `upload_max_filesize` setting |
|`DISPLAY_ERROR`| 1 | `display_errors` to show php errors |
|`STARTUP_ERROR`| 1 | `display_startup_errors` to display statrtup errors|
|`ASSERTIONS` | 0 | `zend.assertions`, only php7 supported: -1 close, 1 open|

## Usage

* pull image
```
docker pull newfuture/yaf
docker pull newfuture/yaf:fpm
```
* run your yaf app : replace `"/PATH/OF/YAF/APP/"` with your app path , and it will auto detect public path (if exist `public folder` and not exist `index.php` ,use the `public` as web root)
```bash
docker run -it --rm -p 1122:80 -v "`pwd`":/yaf newfuture/yaf
```
* run in background
```bash
docker run -d -p 1122:80 -v "`pwd`":/yaf newfuture/yaf
```
* using php5
```bash
docker run -it --rm -p 1122:80 -v "`pwd`":/yaf newfuture/yaf:php5
```

## Build

### 构建说明

Build with travis

1. 注册 https://www.travis-ci.org/ , 使用 github账户授权登录

2. 同步后在 `Settings` 页面的仓库列表中开启Github上的项目 (https://www.travis-ci.org/account/repositories)

3. 使用当前项目 `master`分支提交到自己的仓库中

4. 默认情况下 travis 会帮你构建

5. 提交前需要准备一个 secure 加密参数


### 配置 secure 参数

token 方式连接github, 使 travis 可以将打包好的项目方到github仓库中


1. 本机安装 travis  

```sh
$ gem install travis
```

2. 登录 

```sh
$ travis login 
# 根据提示输入 github的账户密码
```

3. 获取 github 的 access token 

在github的`settings -> Developer settings -> Personal access tokens` 页面 `Generate new token` 生成一个token

4. 加密这个token

本机执行 
```sh
# 项目目录下执行
cd /home/yaf-docker

# 其中 xxx 即为在github中生成的token
travis encrypt GH_TOKEN=xxx
```

5. 加密后得到一个字符串, 替换 `.travis.yml` 中的 `- secure: "xxx" ` 内容

```
- secure: "xxx"
```

6. 提交修改，等待 `travis` 构建

7. 登录`travis` 查看构建日志可了解更多构建信息


## Nginx 配置

php-fpm 版本请参考以下nginx配置;
需要注意 root 目录配置, docker内的工作目录是 `/yaf`, 也可以写 `fastcgi_param` 来指定目录位置

```sh
$ docker run --name fpm-test -it --rm -p 9000:9000 -v /Users/Jainboo/Sites/yourdomain.com:/yaf jianboo/yaf:latest
```

```
server
  {
    listen 80;
    server_name yourdomain.com;
    index index.html index.php;
    root /home/wwwroot;

    location / {
    }

    location ~ .*\.(php|php5)?$ {
        # WORKDIR
        root /yaf;

        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        include fastcgi.conf;
        include fastcgi_params;
    }
  }
```
