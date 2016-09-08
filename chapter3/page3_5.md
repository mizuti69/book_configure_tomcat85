# システムログ設定

**システムログ**  

不要なログは出力さえれないよう設定  
ホストベースでtomcatの設定を行っていないければ出力しないようコメントアウトしておく。  

```
# vim ${CATALINA_HOME}/conf/logging.properties
handlers = 1catalina.org.apache.juli.AsyncFileHandler, 3manager.org.apache.juli.AsyncFileHandler, java.util.logging.ConsoleHandler

#2localhost.org.apache.juli.AsyncFileHandler.level = FINE
#2localhost.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
#2localhost.org.apache.juli.AsyncFileHandler.prefix = localhost.

#4host-manager.org.apache.juli.AsyncFileHandler.level = FINE
#4host-manager.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
#4host-manager.org.apache.juli.AsyncFileHandler.prefix = host-manager.

#org.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/host-manager].level = INFO
#org.apache.catalina.core.ContainerBase.[Catalina].[localhost].[/host-manager].handlers = 4host-manager.org.apache.juli.AsyncFileHandler
```

**アクセスログ**  
tomcat側でもアクセスログを出力するよう設定  
ログフォーマットをapacheと同様に設定します。  

```xml
<!-- # vim ${CATARINA_HOME}/conf/server.xml -->
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
       prefix="tomcat-access" suffix=".log"
       pattern='%h %l %u %t "%r" %s %b "%{Referer}i" "%{User-Agent}i" %D' />
```

**標準出力ログ**  
JSVCで起動した際のtomcat標準出力ロギング設定  
起動スクリプト内の起動オプションに記載します。  

```
# vim tomcat.sh
-outfile $LOGDIR/catalina.out \
-errfile '&1' \
```

**ログローテート**  
tomcatシステムログ以外は自動でローテートされないため、ローテート設定を忘れずに  

```
# vim /etc/logrotate.d/tomcat
/opt/app/tomcat/logs/catalina.out
{
  daily
  copytruncate
  notifempty
  su tomcat tomcat
}
```
