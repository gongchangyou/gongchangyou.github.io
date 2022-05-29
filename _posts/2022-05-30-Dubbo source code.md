---

layout: post
title: "dubbo"
date: 2022-05-30 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# Dubbo 源码解析

### bean加载，实例化

参考文章 [https://blog.csdn.net/leisurelen/article/details/107078066](https://blog.csdn.net/leisurelen/article/details/107078066)



示例代码仓库: [https://github.com/gongchangyou/bean](https://github.com/gongchangyou/bean)



示例代码的例子

```
@Slf4j
@Component
public class AbstractAnnotationBeanPostProcessor implements BeanDefinitionRegistryPostProcessor, SmartInstantiationAwareBeanPostProcessor {
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
                    //可以在这里做一些初始化field的 操作
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



问题1： 当服务端切换ip时（比如蓝绿升级），客户端 invokers（里面有ip/port）是如何更新的？

回答1: org.apache.dubbo.registry.nacos.NacosRegistry 会订阅nacos事件,每5分钟poll一次. 当nacos EventDispatcher中的listener.onEvent 触发时，会去变更RouterChain中的 invokers.
    ![]({{ site.baseurl}}/images/202205/WechatIMG246.png){: width="800" }
    
![]({{ site.baseurl}}/images/202205/WechatIMG247.png){: width="800" }

### 客户端调用



