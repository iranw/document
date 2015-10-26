### 在ubuntu64 安装 java环境



###### 1、安装关联库
```
$sudo apt-get install libboost-dev libboost-test-dev libboost-program-options-dev libboost-system-dev libboost-filesystem-dev libevent-dev automake libtool flex bison pkg-config g++ libssl-dev
```

###### 2、下载软件包
学下载java jdk,`jdk-8u60-linux-x64.tar.gz`
url:http://www.oracle.com/technetwork/java/javase/downloads/index.html
```
$tar -zxvf jdk-8u60-linux-x64.tar.gz 
$sudo cp -rf jdk1.8.0_60 /usr/local/
```

###### 3、环境变量设置
设置`/etc/profile`配置文件 末尾添加以下代码
```
#Set Java environment
JAVA_HOME=/usr/local/jdk1.8.0_60
PATH=$JAVA_HOME/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME
export PATH
export CLASSPATH
```

###### 4、更新profile
```
iranw@ubu64:/usr/local$ source /etc/profile
iranw@ubu64:/usr/local$ echo $JAVA_HOME
/usr/local/jdk1.8.0_60
iranw@ubu64:/usr/local$ echo $PATH
/usr/local/jdk1.8.0_60/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
iranw@ubu64:/usr/local$ java -version
java version "1.8.0_60"
Java(TM) SE Runtime Environment (build 1.8.0_60-b27)
Java HotSpot(TM) 64-Bit Server VM (build 25.60-b23, mixed mode)

iranw@ubu64:/usr/local$ javac -version
javac 1.8.0_60
```










