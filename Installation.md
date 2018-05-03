#

前提：需要先安装JDK

1.打开[下载页面](https://tomcat.apache.org/download-90.cgi)
<br>
2.选择
<br>
3.


路径：`/usr/local/apache-tomcat-9.0.6`
4.修改权限
```
$ /usr/local/apache-tomcat-9.0.6/bin/catalina.sh run
-bash: /usr/local/apache-tomcat-9.0.6/bin/catalina.sh: Permission denied

$ sudo chmod 755 /usr/local/apache-tomcat-9.0.6/bin/*.sh
```



#### 参考资料：
[RUNNING.txt](http://tomcat.apache.org/tomcat-9.0-doc/RUNNING.txt)
