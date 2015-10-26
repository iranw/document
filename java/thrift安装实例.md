使thrift支持java 必须安装ant组件
sudo apt-get install ant

./configure PHP_CONFIG_PREFIX='/usr/local/php/lib/php.ini' PHP_PREFIX='/usr/local/php' JAVAC='/usr/local/jdk1.8.0_60/bin/javac' --with-boost

./configure --prefix=/usr/local/thrift PHP_CONFIG_PREFIX='/usr/local/php/lib/php.ini' PHP_PREFIX='/usr/local/php' JAVAC='/usr/local/jdk1.8.0_60/bin/javac' --with-boost
