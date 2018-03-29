[Image1]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img1.png
[Image2]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img2.png
[Image3]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img3.jpg
[Image4]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img4.jpg
[Image5]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img5.jpg
[Image6]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img6.jpg
[Image7]:https://github.com/zhu-wen/spring-clould/blob/master/spring-cloud-zookeeper/image/img7.jpg
## Spring Cloud中使用zookeeper作为服务注册中心
### 1.windows环境下安装zookeeper
#### 下载
[zookeeper官网下载地址](http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.10/)
#### 解压
![Image1]
#### 启动
命令：zkService.cmd

![Image2]

#### 查看节点
另开一个窗口，启动zk客户端：zkCli -server 127.0.0.1:2181

![Image3]

查看节点：ls /services

![Image4]

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
public class ZkDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(ZkDemoApplication.class, args);
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
命令：ls /services

![Image5]

#### 访问接口查看节点信息
地址:http://localhost:8080/zookeeper/services

![Image6]

![Image7]

