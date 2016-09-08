# バージョン情報の隠蔽
Tomcatのシステムページやヘッダー等にTomcatバージョン情報が表示されないようにする。  

```
# cd ${CATALINA_HOME}/lib
# jar xf catalina.jar org/apache/catalina/util/ServerInfo.properties
```

展開したアプリケーションファイルを修正  

```
# vim org/apache/catalina/util/ServerInfo.properties
server.info=
server.number=
server.built=
```

最コンパイル  

```
# jar uf catalina.jar org/apache/catalina/util/ServerInfo.properties
```

バージョン情報が出力されないことを確認  

```
# ../bin/version.sh
```

完了後、展開したファイルは削除しておく。  

```
# rm -rf org
```
