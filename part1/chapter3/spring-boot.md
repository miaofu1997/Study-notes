# 3.1 Spring boot

### Project Structure

* `src/main/java` 程序开发以及主程序入口
* `src/main/resources` 配置文件
* `src/test/java` 测试程序

### Root package Structure

```java
+- cn.cecii.scp
  +- ScpWebApplication.java // main method. the entrance.
  |
  +- model
  |  +- ScpDic.java // Dictionary class
  |
  +- service
  |  +- ILoginService.java // Login service
  |  +- Impl
  |    +- LoginServiceImpl.java // Implimentation
  |
  +- controller
  |  +- DemoController.java
  |  +- LoginController.java
  |
  +- mapper
  |  +- ScpDicMapper.java // Similar to DAO in Spring MVC
  |
```

















