# セッション管理
Tomcatがデフォルトで利用するアプリケーションセッションタイムアウト値を設定します。  
アプリケーション側で用意している`web.xml`等で定義してある場合はとしらが優先されますが、  
定義が無い場合tomcatの値が適用されてしまうこともあるため注意します。  

```xml
<!-- # vim ${CATARINA_HOME}/conf/web.xml -->
<!-- ==================== Default Session Configuration ================= -->
<!-- You can set the default session timeout (in minutes) for all newly   -->
<!-- created sessions by modifying the value below.                       -->
	<session-config>
			<session-timeout>5</session-timeout>
	</session-config>
```
