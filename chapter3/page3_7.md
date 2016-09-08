# 自動デプロイ
WARアプリケーションを配置した際に、自動デプロイ、展開されるよう設定します。  

```xml
<!-- # vim ${CATARINA_HOME}/conf/server.xml -->
<Host name="localhost"  appBase="webapps"
      unpackWARs="true" autoDeploy="true">
```

* unpackWARs : WARファイルを自動で展開します。  
* autoDeploy : `appBase`配下のWARファイルを自動デプロイします。  

自動でデプロイは  

* タイムスタンプが更新された
* 新規のWARが配置された

場合デプロイされます。  

注意しなければならない点としてはtomcat再起動時に`autoDeploy`は動かないという事。  
tomcatを停止し新しいWARを配置しても起動時にデプロイされるのは既存の展開されたアプリケーションになります。  
その場合WARのタイムスタンプより展開アプリケーションのタイムスタンプが阿多rしくなるため、  
新しいWARファイルはデプロイされなくなってしまいます。  

tomcatの停止を含むリリースを行いたい場合、  
停止後に`appBase`配下を削除（クリーニング）してから配備、起動するか、  
配備、自動デプロイ後に再起動するようにする。  
