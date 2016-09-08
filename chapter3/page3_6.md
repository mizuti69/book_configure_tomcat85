# 不要なアプリケーションの削除
Tomcatにはデフォルトで管理用も含め幾つかのデフォルトアプリが配置されています。  
基本的には全て不要ですので、必要ない限り削除します。
`tomcat-manager`を利用してアプリケーションのデプロイを行なう場合は残します。  

```
# cd ${CATALINA_HOME}/webapps
# rm -rf ROOT docs examples host-manager
```
