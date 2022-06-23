---

layout: post
title: "dubbo"
date: 2022-05-30 09:25:06 +0800
comments: true
category: java
tag: [java]

---

# Dubbo 源码解析

### bean加载，实例化

参考文章 [https://blog.csdn.net/leisurelen/article/details/107078066](https://blog.csdn.net/leisurelen/article/details/107078066)



示例代码仓库: [https://github.com/gongchangyou/bean](https://github.com/gongchangyou/bean)



BeanPostProcessor 的具体实现

```
@Slf4j
public class AbstractAnnotationBeanPostProcessor implements BeanDefinitionRegistryPostProcessor, SmartInstantiationAwareBeanPostProcessor {
    public static final String BEAN_NAME = "abstractAnnotationBeanPostProcessor";
    @Override
    @Nullable
    public PropertyValues postProcessProperties(PropertyValues pvs, Object bean, String beanName)
            throws BeansException {
        final List<Object> elements = new LinkedList<>();
        ReflectionUtils.doWithFields(bean.getClass(), new ReflectionUtils.FieldCallback() {
            @Override
            public void doWith(Field field) throws IllegalArgumentException, IllegalAccessException {
                if (field.getAnnotation(Dubbo.class)!=null) {
                    elements.add(field);
                    //可以在这里做一些根据初始化field的 操作
                    field.setAccessible(true);
                    field.set(bean, 3L);
                }
            }
        });
        log.info("beanName={} list={}",beanName,elements);
        return null;
    }
}
```

将BeanPostProcessor注册到 BeanDefinition中:

![]({{ site.baseurl}}/images/202205/WechatIMG248.png){: width="800" }

只需要在application上添加 @Import 注解即可

![]({{ site.baseurl}}/images/202205/WechatIMG249.png){: width="800" }





### 客户端调用

自己整理了下客户端调用流程: [dubbo client.drawio]({{ site.baseurl}}/images/202205/dubbo client.drawio)
![]({{ site.baseurl}}/images/202205/dubbo_client.drawio.png){: width="800" }


问题1： 当服务端切换ip时（比如蓝绿升级），客户端 invokers（里面有ip/port）是如何更新的？

回答1: org.apache.dubbo.registry.nacos.NacosRegistry 会订阅nacos事件,每5分钟poll一次. 当nacos EventDispatcher中的listener.onEvent 触发时，会去变更RouterChain中的 invokers.
![]({{ site.baseurl}}/images/202205/WechatIMG246.png){: width="800" }
    
![]({{ site.baseurl}}/images/202205/WechatIMG247.png){: width="800" }

### 服务端bean加载

具体实现的 BeanDefinitionRegistryPostProcessor 是 ServiceClassPostProcessor. 他扫描packagesToScan 中类，解析DubboService 注解。

那么他是何时被注册的呢？ 跟客户端bean加载一样，@Import (DubboComponentScanRegistrar.class)  . 

DubboComponentScanRegistrar中会注册 ServiceClassPostProcessor的子类 ServiceAnnotationBeanPostProcessor（已经不推荐使用了）



### 服务端逻辑

流程图: 
[dubbo_server.drawio]({{ site.baseurl}}/images/202205/dubbo_server.drawio)

![]({{ site.baseurl}}/images/202205/dubbo_server.drawio.png){: width="800" }

问题1：二进制字节流 如何解析 serviceName methodName parameters ?


回答1：DecodeHandler 将 inputStream中的 buffer 转换成targetServiceUniqueName, methodName, arguments等
具体逻辑在 DecodeableRpcInvocation.decode(Channel channel, InputStream input) 中，应该就是dubbo协议的解析 其中in这个临时变量是根据channel中的serializationType 中确定的，这个也是SPI加载的， 默认是Hessian2



![]({{ site.baseurl}}/images/202205/WechatIMG253.png){: width="800" }

问题2： dubbo在收到报文后，如何给到对应的proxy去执行方法？

回答2:   DubboProtocol， 方法 getInvoker , 根据serviceKey(形如 dubbo-demo/com.braindata.dubbodemo.intf.StuRpcService:1.0.0:62604)  ，去exporterMap 获取对应的value，就是invoker了
![]({{ site.baseurl}}/images/202205/WechatIMG254.png){: width="800" }

问题3： Filter是如何添加进Wrapper的？

回答3:    ProtocolFilterWrapper 类      List<Filter> filters = ExtensionLoader.getExtensionLoader(Filter.class).getActivateExtension(invoker.getUrl(), key, group);



问题4： 如何处理粘包和拆包？

回答4： 参考[https://yestermorrow.github.io/2018/04/10/dubbo%E7%B3%BB%E5%88%97%E4%B9%8B%E7%B2%98%E5%8C%85%E5%92%8C%E6%8B%86%E5%8C%85/](https://yestermorrow.github.io/2018/04/10/dubbo%E7%B3%BB%E5%88%97%E4%B9%8B%E7%B2%98%E5%8C%85%E5%92%8C%E6%8B%86%E5%8C%85/)


我们以netty4/NettyCodecAdapter 为例。内部类 InternalDecoder.decode 方法


拆包的处理?

```
// 关键字 返回这个表示没有在当前包检测到end。 需要等待下个包
Codec2.DecodeResult.NEED_MORE_INPUT
```


粘包的处理？ 

其中 saveReaderIndex 可以记录message的读索引, 如果粘包，decode部分结果后, 当前的读游标肯定不是0，则可以从 message的 readerIndex 开始读取. 下面代码可以和原先的netty/NettyCodecAdapter比对下，省去了一些关于拆包时拼接的逻辑 比如 buffer.readableBytes() + input.readableBytes(); 
![]({{ site.baseurl}}/images/202205/WechatIMG277.png){: width="800" }

