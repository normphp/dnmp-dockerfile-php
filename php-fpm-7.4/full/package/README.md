# https://pecl.php.net/ package
* 各PHP版本的dockerfile构建需要的本地包文件

## 构建需要的扩展包
* 由于众所周知的原因，在构建过程中通过命令行下载需要的安装包会非常缓慢，可通过百度云下载需要的文件放在dockerfile同级目录的package目录下。
    * 链接：https://pan.baidu.com/s/1SGDEHqGZB-NTyqTJV_Rm3A 提取码：norm
    * 非必要可选择性下载：ImageMagick.tar.gz
* 原始下载地址：
    * mongodb-1.9.0.tgz   https://pecl.php.net/get/mongodb-1.9.0.tgz
    * do_sqlsrv-5.8.1     https://pecl.php.net/get/pdo_sqlsrv-5.8.1.tgz
    * xlswriter-1.3.7     https://pecl.php.net/get/xlswriter-1.3.7.tgz
    * imagick-3.4.4       https://pecl.php.net/get/imagick-3.4.4.tgz
    * xdebug-3.0.2        https://pecl.php.net/get/xdebug-3.0.2.tgz
    * redis-5.3.2         https://pecl.php.net/get/redis-5.3.2.tgz
    * swoole-4.6.1        https://pecl.php.net/get/swoole-4.6.1.tgz
    * ImageMagick         https://download.imagemagick.org/ImageMagick/download/ImageMagick.tar.gz
    * libzip-1.7.3        https://libzip.org/download/libzip-1.7.3.tar.gz
    * libssh2-1.9.0       https://www.libssh2.org/download/libssh2-1.9.0.tar.gz