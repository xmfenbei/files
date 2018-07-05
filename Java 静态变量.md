
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
