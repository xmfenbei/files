
**原则：静态成员变量名称不能和赋值的参数名称同名。**

## 方法一
```
@Component
public class EsProperties {
    public static String clusterName;
    public static String clusterIP;
    public static Integer clusterPort;

    @Value("${es.clusterName}")
    public void setEsClusterName(String esClusterName) {
        clusterName = esClusterName;
    }

    @Value("${es.clusterIP}")
    public void setEsClusterIP(String esClusterIP) {
        clusterIP = esClusterIP;
    }

    @Value("${es.clusterPort}")
    public void setEsClusterPort(Integer esClusterPort) {
        clusterPort = esClusterPort;
    }
}
```

## 方法二
```
@Component
public class EsProperties {
    public static String clusterName;
    public static String clusterIP;
    public static Integer clusterPort;
    
    @Value("${es.clusterName}")
    private String privateClusterName;

    @Value("${es.clusterIP}")
    private String privateClusterIP;

    @Value("${es.clusterPort}")
    private Integer privateClusterPort;

    @PostConstruct
    public void init() {
        clusterIP = privateClusterIP;
        clusterName = privateClusterName;
        clusterPort = privateClusterPort;
    }
}
```

总结：建议采用方法一。

学习@PostConstruct注解的用法。


#### 参考资料：
[Spring – Inject value into static variables](https://www.mkyong.com/spring/spring-inject-a-value-into-static-variables/)

[注解@PostConstruct与@PreDestroy详解及实例](https://blog.csdn.net/wo541075754/article/details/52174900)

[Spring静态变量注入](https://blog.csdn.net/Healist/article/details/78658348)
