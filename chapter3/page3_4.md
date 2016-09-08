# DB接続設定

**DBコネクターの用意**  
アプリケーションからDBへ接続するために利用するコネクターライブラリを用意します。  
JDKにある標準のコネクター以外が必要な場合ライブラリに追加します。

下記はMySQL用のコネクターを用意する場合になります。  
Mysqlのコネクターを用意する場合はOracle Mysqlコミュニティーサイトからダウンロードします。  
http://dev.mysql.com/downloads/connector/j/  

ダウンロード後ライブラリディレクトリに配置し、起動スクリプト等から読み込むよう指定しておきます。  

```
# ${CATALINA_HOME}/lib/mysql-connector-java-5.X.XX-bin.jar
```

```
# vim tomcat.sh
-classpath ${CATALINA_HOME}/lib/mysql-connector-java-5.1.39-bin.jar
```

**JNDIデータソース設定**  
Tomcat側でDBへの接続情報を設定する方法になります。  

```xml
<!-- # vim ${CATARINA_HOME}/conf/context.xml -->
<Resource driverClassName="com.mysql.jdbc.Driver" auth="Container"
   factory="org.apache.tomcat.jdbc.pool.DataSourceFactory"
   name="jdbc/dsname" type="javax.sql.DataSource"
   username="dbuser" password="passwd"
   initialSize="2" maxTotal="15" minIdle="2" maxIdle="10"  maxWaitMillis ="10000"
   removeAbandoned="true" removeAbandonedTimeout="300" logAbandoned="true"
   timeBetweenEvictionRunsMillis="300000" testOnBorrow="true" validationQuery="select 1"
   url="jdbc:mysql://192.168.100.11:3306/dbname?characterEncoding=utf8&amp;zeroDateTimeBehavior=convertToNull"
   />
```

* name : JNDI接続子名を設定
* username : DB接続ユーザー
* password : DB接続ユーザーのパスワード
* initialSize : 初期プールサイズ
* maxTotal : 最大プールサイズ
* minIdle : 最小アイドル接続数
* maxIdle : 最大アイドル接続数
* maxWaitMillis : 接続待ち地時間（ミリ秒）
* removeAbandoned : 接続が切れたプールセッションを破棄する
* removeAbandonedTimeout : 破棄されるまでの秒数（秒）
* logAbandoned : 破棄された処理のスタックトレースを出力する
* timeBetweenEvictionRunsMillis : アイドル状態のチェック間隔（ミリ秒）
* testOnBorrow : プール接続ができるか接続時に検証する
* validationQuery : 検証時に利用するSQL
https://tomcat.apache.org/tomcat-8.0-doc/jndi-resources-howto.html
