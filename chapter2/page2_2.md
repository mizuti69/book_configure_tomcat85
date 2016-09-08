# Tomcatのインストール
apache-tomcatプロジェクトサイトより最新のtomcatパッケージをダウンロードします。  
http://tomcat.apache.org/download-80.cgi  

ダウンロードしたパッケージを、アプリケーションをインストールしたいディレクトリに展開します。  

```
# tar xzf apache-tomcat-8.X.XX.tar.gz
```

インストールしたTomcatは運用しやすいようリンクを作成して管理します。  

```
# ln -s apache-tomcat-8.X.XX tomcat
```

サービスユーザーの作成  
ユーザー設計に基づき、tomcat起動サービスユーザーを作成します。  

```
# groupadd –g 1200 tomcat
# useradd -u 1200 -g tomcat -c “Tomcat Service" -M –s /sbin/nologin tomcat
# chown tomcat: ./tomcat -R
```
