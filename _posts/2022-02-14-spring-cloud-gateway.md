---
layout: post
title: "spring-cloud-gateway"
date: 2022-02-10 10:25:06 +0800
comments: true
category: java
tag: [java]
---



#  Spring-cloud-gateway

## 路由规则如果配置在application.yaml中，那么每次新增路由都需要部署重启gateway，所以这个路由规则必须动态读取，例如存放到nacos中

代码仓库:   [https://github.com/gongchangyou/gateway](https://github.com/gongchangyou/gateway)

http服务 [https://github.com/gongchangyou/dubbo-demo-producer](https://github.com/gongchangyou/dubbo-demo-producer)

RouteDefinitionWriter 负责修改路由， RouteDefinitionLocator 接口只有一个方法 getRouteDefinitions，获取所有路由。

```
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-gateway</artifactId>
		</dependency>
```



![code]({{ site.baseurl}}/images/202202/1644386286460.jpg){: width="1000" }



重点是 routeDefinitionWriter.save(Mono.just(routeDefinition)).subscribe();

```
//        this.publisher.publishEvent(new RefreshRoutesEvent(this)); 这句好像没啥用
```





## Nacos: 代码只需要添加nacos的listener，又因为数据变更时只会执行一次回调，没有心跳，我们需要自行编写心跳逻辑



```
<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
			<version>2.2.7.RELEASE</version>
			<exclusions>
				<exclusion>
					<groupId>org.apache.httpcomponents</groupId>
					<artifactId>httpclient</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
			<version>2.2.7.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-loadbalancer</artifactId>
		</dependency>
```


```
@Component
@Slf4j
public class NacosRouteDynamicDataSource implements ApplicationRunner {
    @Autowired
    private NacosConfigManager configManager;

    private ConfigService configService;
    @PostConstruct
    void init () {
        configService = configManager.getConfigService();
    }
    /**
     * Callback used to run the bean.
     *
     * @param args incoming application arguments
     * @throws Exception on error
     */
    @Override
    public void run(ApplicationArguments args) throws Exception {
        log.info(args.toString());
        //监听nacos配置变化
        dynamicRouteByNacosListener("gateway-dynamic-route-rule.json", "gateway-dynamic-route-rule");
        //心跳 顺便 初始化
        while (true) {
            String configInfo = configService.getConfig("gateway-dynamic-route-rule.json", "gateway-dynamic-route-rule", 4000);
            log.info("configInfo = {}", configInfo);
            Thread.sleep(2000);
        }
    }

    /**
     * 监听nacos的配置
     * @param dataId
     * @param group
     */
    public void dynamicRouteByNacosListener (String dataId, String group){
        try {
            configService.addListener(dataId, group, new Listener()  {
                @Override
                public void receiveConfigInfo(String configInfo) {
                    log.info("进行网关更新:\n\r{}",configInfo);
                }
                @Override
                public Executor getExecutor() {
                    log.info("getExecutor\n\r");
                    return null;
                }
            });
        } catch (NacosException e) {
            log.error("从nacos接收动态路由配置出错!!!",e);
        }
    }
}

```



在nacos手动配置路由

![nacos config]({{ site.baseurl}}/images/202202/1644471609530.jpg){: width="1000" }