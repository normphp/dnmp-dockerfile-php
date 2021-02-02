# dnmp-dockerfile-php
* 各PHP版本的dockerfile构建文件

## 构建需要的扩展包
* 由于众所周知的原因，在构建过程中通过命令行下载需要的安装包会非常缓慢，可通过百度云下载需要的文件放在dockerfile同级目录。
    * 链接：https://pan.baidu.com/s/1SGDEHqGZB-NTyqTJV_Rm3A 提取码：norm
    * 非必要可选择性下载：ImageMagick.tar.gz
    
## 基础说明
* 2021-01-29开始：
    * 不再使用ubuntu基础镜像的php:7.4-fpm官方镜像改为使用alpine基础镜像的官方php:7.4-fpm-alpine，镜像从800M左右缩小为200M左右
    * 开始全面支持 linux/amd64,linux/arm64 CPU架构
        *  php-fpm-[8.0]-universal 暂时只支持 linux/amd64,linux/arm64 CPU架构
        *  php-fpm-[7.1|7.3|7.4]-universal 支持 linux/amd64,linux/arm64,linux/ppc64le,linux/s390x,linux/386 CPU架构
    * 因支持多CPU架构的镜像构建编译需要大量的测试、消耗大量的CPU内存资源（构建一个php-fpm-7.4-universal 4核8线程 10G内存性能消耗殆尽耗时超过2小时），综合考虑以后不再支持构建PHP7.1 PHP7.2新镜像，后期只持续更新发布7.4+、8.0+
    * 关于产品线：
        * 同样由于多CPU架构镜像构建编译需要的资源和精力做为维护者个人精力有限放弃之前的universal、swoole、full中的swoole版本
        * full、universal版本的差异主要在php扩展支持不同详情见下一个大标题目录
        * full:安装了90%的常用php组件扩展
        * universal:只在php官方镜像基础上安装 pdo_mysql、redis、xlswriter、gd、OPcache、zip、intl、gmp等常用组件扩展
    
## 各版本组件扩展区别
*   [PHP8.0-universal] bcmath,Core,ctype,curl,date,dom,fileinfo,filter,ftp,gd,gmp,hash,iconv,igbinary,intl,json,libxml,mbstring,mysqlnd,openssl,pcre,PDO,pdo_mysql,pdo_sqlite,Phar,posix,readline,redis,Reflection,session,SimpleXML,sockets,sodium,SPL,sqlite3,standard,tokenizer,xlswriter,xml,xmlreader,xmlwriter,Zend OPcache,zip,zlib
*   [PHP7.4-universal] bcmath,Core,ctype,curl,date,dom,fileinfo,filter,ftp,gd,gmp,hash,iconv,igbinary,imagick,intl,json,libxml,mbstring,mysqlnd,openssl,pcre,PDO,pdo_mysql,pdo_pgsql,pdo_sqlite,Phar,posix,readline,redis,Reflection,session,SimpleXML,sockets,sodium,SPL,sqlite3,ssh2,standard,swoole,tokenizer,xdebug,xlswriter,xml,xmlreader,xmlwriter,Zend OPcache,zip,zlib
*   [PHP7.4-full] bcmath,Core,ctype,curl,date,dom,fileinfo,filter,ftp,gd,gmp,hash,iconv,igbinary,imagick,intl,json,libxml,mbstring,mysqlnd,openssl,pcre,PDO,pdo_mysql,pdo_pgsql,pdo_sqlite,Phar,posix,readline,redis,Reflection,session,SimpleXML,sockets,sodium,SPL,sqlite3,ssh2,standard,swoole,tokenizer,xdebug,xlswriter,xml,xmlreader,xmlwriter,Zend OPcache,zip,zlib

## 在universal镜像上进行自定义组件扩展
* 考虑到大部分项目是不需要full镜像那么多组件扩展，您可以在universal的构建dockerfile基础上使用现有的universal镜像自定义构建
    * 在对应php-fpm-[7.4|8.0]\diy\目录中有参考dockerfile（注意下载拷贝安装包文件到目录中）
    * 常规构建命令（参考具体构建知识自行谷歌、百度）：
      * 在dockerfile目录执行：docker build -t [你自定义的镜像名称] .
      * 使用diy构建的镜像测试启动一个容器：docker run --name  [你自定义的容器名称]  -d  [上一步你自定义的镜像名称]
      * 进入容器：docker exec -it  [上一步你自定义的容器名称]  /bin/sh
      * 注意：镜像系统为alpine部分命令行操作与ubuntu不同
    * 多CPU架构镜像构建
      * 建议在x86架构 64位 CPU 机器上进行构建（经尝试这样构建支持的cpu架构最多）
      * 建议构建机器磁盘空间至少有20G（多CPU架构构建时还产生大量构建缓存可使用docker buildx prune 命令清除）、内存至少10G、CPU至少4核心
      * 建议下在常规模式下进行构建测试再进行多CPU架构镜像构建（多CPU架构镜像构建时排错困难）
      * 具体构建方法
        * 需要docker版本至少 20
        * 查看当前支持的cpu架构：docker buildx ls
        * 我们当前使用的是默认的 builder，它基本上是旧的 builder，让我们创建一个新的 builder，它使我们能够访问一些新的多架构结构功能。
            * docker buildx create --use --name mybuilder （命令只需要执行一次）
        * 更新查看 builder：docker buildx inspect --bootstrap （命令通常只需要执行一次可能需要执行很长时间请耐心等待）
        * 测试构建多架构镜像，不推送到DockerHub ：
            * docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 -t [你自定义一个镜像名称]  .  （ --platform 参数为上一步命令的结果，可根据需要选择性的删除一些CPU架构，使用的架构越多消耗的性能
              根据编译时间、出现构建编译错误的概率越多）
        * 构建多架构镜像，并推送到DockerHub（需要先登录）：
            * docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 -t donhui/multiarch --push .
    * 篇幅有限更多docker 镜像构建知识请自行谷歌、百度

## alpine操作系统常规知识点
* 包管理命令
    * 安装包： apk --no-cache add   [包名] 
    * 删除包： apk del   [包名] 
* 包资源镜像加速
    * 众所周知原因默认的国外镜像下载速度生不如死，我们需要修改镜像源为国内源：
        * 修改方法参考php-fpm-7.4\full\dockerfile:15行
        * 使用normphp/dnmp-php:php-fpm-[7.4|8.0]-[universal|full]系列镜像做基础镜像时，镜像源已经修改为国内源了不需要重复操作。
        * 如碰到apk --no-cache add 包时提示资源包不存在：
          * 可尝试浏览器打开https://pkgs.alpinelinux.org/packages ，在搜索搜索包名
          * 加入指定的“Branch”和“Repository”的源到http://mirrors.ustc.edu.cn/alpine/[Branch]/[Repository]，就可以apk add(修改加入方法参考php-fpm-7.4\full\dockerfile:15行)
* 进入容器：
    *  alpine系统与ubuntu系统命令行不同，shell执行目录为 /bin/sh
    *  docker exec -it [上一步你自定义的容器名称]  /bin/sh
    *  docker-compose exec  [SERVICE]  /bin/sh
* 篇幅有限更多 alpine操作系统常 规知识点请自行谷歌、百度