## Spring Cloud中使用zookeeper作为服务注册中心
### 1.windows环境下安装zookeeper
#### 下载
zookeeper官网下载地址
#### 解压
配图
#### 启动
zkService.cmd
配图
#### 查看节点
启动zookeeper客户端进行连接：zkCli -server 127.0.0.1:2181
配图
查看节点：命令
配图
### 2.创建Spring Cloud工程，并在zookeeper中注册
#### maven依赖
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
</dependency>
```
#### 启动类
```
@SpringBootApplication
@EnableDiscoveryClient
public class SpringCloudZookeeperClientAppApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringCloudZookeeperClientAppApplication.class, args);
    }
}
```
#### 配置文件application.properties
```
## 配置应用名称
spring.application.name=spring-cloud-zookeeper-client-app
## 配置服务端口
server.port=8080
## 关闭安全控制
management.security.enabled=false
## 配置zookeeper地址
spring.cloud.zookeeper.connect-string=127.0.0.1:2181
```
#### 使用DiscoveryClient获取注册服务列表API
```
@RestController
@RequestMapping("/zookeeper")
public class ZookeeperController {

    @Value("${spring.application.name}")
    private String instanceName;

    private final DiscoveryClient discoveryClient;

    @Autowired
    public ZookeeperController(DiscoveryClient discoveryClient) {
        this.discoveryClient = discoveryClient;
    }

    @GetMapping
    public String hello() {
        return "Hello,Zookeeper.";
    }

    @GetMapping("/services")
    public List<String> serviceUrl() {
        List<ServiceInstance> list = discoveryClient.getInstances(instanceName);
        List<String> services = new ArrayList<>();
        if (list != null && list.size() > 0 ) {
            list.forEach(serviceInstance -> {
                services.add(serviceInstance.getUri().toString());
            });
        }
        return services;
    }
}
```
### 3.查看注册状态
#### 在zookeeper中查看节点信息
命令
配图
#### 访问接口查看节点信息
地址
配图

