---
layout: post
title: "Validation"
date: 2022-10-13 10:25:06 +0800
comments: true
category: java
tag: [java]


---

# Validation

代码仓库: 

[https://github.com/gongchangyou/pdfreader](https://github.com/gongchangyou/pdfreader)



dependency

```
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
        </dependency>
```



annotation

```
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ParamValidation {
}
```





aspect

```
@Slf4j
@Component
@Aspect
@Order(10)
public class ValidationAspect {

    @Autowired
    @Qualifier("executableValidator")
    private ExecutableValidator validator;
//    @Around(value = "within(com.mouse.pdfreader.*) && execution(public * com.mouse.pdfreader.*.*(..)) && @annotation(com.mouse.pdfreader.aop.ParamValidation)")
    @Around(value = "@annotation(paramValidation) ")
    public Object process(ProceedingJoinPoint proceedingJoinPoint, ParamValidation paramValidation) {
        val sw= new StopWatch();
        sw.start("validation");
        Set<ConstraintViolation<Object>> constraintViolations = validator.validateParameters(proceedingJoinPoint.getTarget(), ((MethodSignature)proceedingJoinPoint.getSignature()).getMethod(), proceedingJoinPoint.getArgs());
        sw.stop();
        log.info(sw.prettyPrint());

        if(constraintViolations.size() > 0){
            final String constraintMessage = constraintViolations.stream()
                    .findFirst()
                    .map(ConstraintViolation::getMessage)
                    .orElse("");

            return constraintMessage;
        }

        try {
            return proceedingJoinPoint.proceed();
        } catch (Throwable e) {
            e.printStackTrace();
        }

        return null;
    }
}
```





Config:

```
@Configuration
public class ValidatorConfiguration {
    @Bean(name = "executableValidator")
    ExecutableValidator get() {
        val validatorFactory = Validation.byProvider(HibernateValidator.class).configure().failFast(true).buildValidatorFactory();
        return validatorFactory.getValidator().forExecutables();
    }
}

```



Service

```
@Service
public class TestService {

    @ParamValidation
    public String test(@Valid ParamData param){
        return "success";
    }
}
```



param

```
@Data
@Builder
public class ParamData {
    @Range(min=1,max=10, message = "1-10")
    int age;

    @NotNull(message = "null name")
    String name;

}
```





检查的字段数,1-2个字段 耗时 0.2-0.4ms。  对于高频计算来说，还是耗时较高。

