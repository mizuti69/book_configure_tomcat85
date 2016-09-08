# サーバ設定

**停止ポートの変更**  

Tomcatの利用ポートをデフォルトから変更しておく。  

```
# vim ${CATALINA_HOME}/conf/server.xml
<Server port="8010" shutdown="SHUTDOWN">
```

JSVCでTomcatサービスを制御している場合はこのポートを利用しませんがサーバ上はリッスンしています。  
`catarina.sh`等スクリプト経由でサービス管理する場合は必ず変更しておきます。  

**AJPコネクターの設定**  
AJPコネクター経由でフロントWEBシステムから連携する際に利用するコネクターを設定します。  
必要により複数のコネクターを設定する事も可能です。  
最低現下記の値を設定しておきます。  

```xml
<!-- # vim ${CATALINA_HOME}/conf/server.xml -->
<Connector port="8080" protocol="AJP/1.3"
           proxyName="www.localdomain" proxyPort="443"
           scheme="https" secure="true"
           connectionTimeout="60000" maxThreads="256" />
```

* port : リスニングポートを指定
* proxyName : アプリケーションリダイレクト時にクライアントに返すURL名
* ProxyPort : アプリケーションリダイレクト時にクライアントに返すポート
* scheme : コネクターのプロトコル属性を指定  
* secure : コネクターのプロトコル属性がSSLの場合trueを指定
* connectionTimeout : コネクションタイムアウト値（ミリ秒）
* maxThreads : 最大同時接続数
https://tomcat.apache.org/tomcat-8.0-doc/config/http.html  

**HTTPコネクターの設定**  
HTTPコネクター経由で接続する場合の設定を行います。  
最低現下記の値を設定しておきます。  

```xml
<!-- # vim ${CATALINA_HOME}/conf/server.xml -->
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="30000"
           redirectPort="80" maxThreads="256" />
```

* port : リスニングポートを指定
* ProxyPort : アプリケーションリダイレクト時にクライアントに返すポート
* connectionTimeout : コネクションタイムアウト値（ミリ秒）
* maxThreads : 最大同時接続数
https://tomcat.apache.org/tomcat-8.0-doc/config/http.html  
