# Tomcat起動処理の作成

**JSVCコマンドの作成**  
TomcatサービスはJSVCから起動・停止制御を行ないます。  

```
# cd ${CATALINA_HOME}/bin
# tar xzf commons-daemon-native.tar.gz
# cd commons-daemon-1.0.15-native-src/unix/
# ./configure --with-java=/usr/java/latest
# make
```

作成されたJSVCプログラムを`bin`ディレクトリ配下に配置しておき、  
作成後ソースファイルは削除しておく。  

```
# mv jsvc ../../
# rm -rf commons-daemon-1.0.15-native-src
```

**起動スクリプトの作成**  
JSVCをキックする起動スクリプトを作成  

```bash
# tomcat/bin/tomcat.sh
# SAMPLE
#!/bin/bash
#!/bin/bash
#
. /etc/rc.d/init.d/functions
umask 002
LANG="ja_JP.utf8"
PIDFILE=/var/run/tomcat.pid
LOCKFILE=/var/lock/subsys/tomcat

CATALINA_HOME=/opt/app/tomcat
CATALINA_BASE=/opt/app/tomcat
CATALINA_OPTS="-server -Xmx1024m -Xms1024m -XX:MetaspaceSize=512m -XX:MaxMetaspaceSize=1024m -XX:CompressedClassSpaceSize=512m -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager"
LOGDIR=$CATALINA_HOME/logs

JAVA_HOME=/usr/java/latest
JAVA_GCLOG=$LOGDIR/tomcat_gc.log
JAVA_GC_MAX=5242880
JAVA_GC_LIMIT=2

TOMCAT_GCOPTIONS="-Xloggc:$JAVA_GCLOG -XX:+PrintGCDateStamps -XX:+PrintGCDetails -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=$JAVA_GC_LIMIT -XX:GCLogFileSize=$JAVA_GC_MAX"
TOMCAT_USER=tomcat
TOMCAT_TMP_DIR=/opt/app/tomcat/temp
TOMCAT_LOG="-Djava.util.logging.config.file=$CATALINA_BASE/conf/logging.properties"

DAEMON=$CATALINA_HOME/bin/jsvc

CLASSPATH=\
$JAVA_HOME/lib/tools.jar:\
$CATALINA_HOME/lib/mysql-connector-java-5.1.39-bin.jar:\
$CATALINA_HOME/bin/tomcat-juli.jar:\
$CATALINA_HOME/bin/commons-daemon.jar:\
$CATALINA_HOME/bin/bootstrap.jar:

start(){
    echo -n "Starting jsvc: "
    $DAEMON \
    -pidfile $PIDFILE \
    -user $TOMCAT_USER \
    -home $JAVA_HOME \
    -outfile $LOGDIR/catalina.out \
    -errfile '&1' \
    $TOMCAT_LOG \
    $CATALINA_OPTS \
    $CATALINA_SWITCH \
    $TOMCAT_GCOPTIONS \
    -classpath $CLASSPATH \
    -Dcatalina.home=$CATALINA_HOME \
    -Dcatalina.base=$CATALINA_BASE \
    -Djava.io.tmpdir=$TOMCAT_TMP_DIR \
    org.apache.catalina.startup.Bootstrap

    # To get a verbose JVM
    #-verbose \
    # To get a debug of jsvc.
    #-debug \
    RETVAL=$?
    if [ $RETVAL = 0 ]; then
        echo_success
        touch $LOCKFILE
    else
        echo_failure
    fi
    echo
}

stop(){
    echo -n "Shutting down jsvc: "
    $DAEMON \
    -stop \
    -pidfile $PIDFILE \
    org.apache.catalina.startup.Bootstrap
    RETVAL=$?
    if [ $RETVAL = 0 ]; then
        echo_success
        rm -f $PIDFILE $LOCKFILE
    else
        echo_failure
    fi
    echo
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        stop
        start
        ;;
    status)
        status $DAEMON
        RETVAL=$?
        ;;
    *)
        echo $"Usage: jsvc {start|stop|restart|status}"
        exit 1
        ;;
esac
```

**自動起動設定**  
作成した起動・停止スクリプトをsystemdに登録し、自動起動するよう設定します。  

システムターゲットに格納するユニットファイルの作成  

```
# vim /usr/lib/systemd/system/tomcat.service
[Unit]
Description = Tomcat service
After=syslog.target network.target

[Service]
ExecStart = /opt/app/tomcat/bin/tomcat.sh start
ExecStop = /opt/app/tomcat/bin/tomcat.sh stop
Type = forking
SuccessExitStatus = 143

[Install]
WantedBy = multi-user.target
```

ユニットファイルから作成したスクリプトを呼び出しています。  

systemdへの登録・自動起動設定  

```
# systemctl enable tomcat.service
```

以上でtomcatの自動起動、停止制御設定は完了です。  
systemd登録後にユニットファイルを変更した場合は再読み込みさせる必要があります。  

```
# systemctl daemon-reload
```
