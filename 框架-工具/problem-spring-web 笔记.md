## problem-spring-web 笔记

### 核心原理

* 底层依赖 Spring MVC  ControllerAdvice

##### ControllerAdvice

* 看名字就知道，类似 AOP Advice 功能，但是底层实现不是动态代理，依赖

###### 三种功能

* @ModelAttribute

  > * 修改 Model 参数

* @ExceptionHandler

  > * 处理全局异常

* @InitBinder

  > * 全局参数的绑定
  > * 

##### Problem 使用 @ExceptionHandler


* 定义了 核心类 `ProblemHandling` , 该接口继承了定好的各种异常类型的ExceptionHandler 默认实现

* ```java
  public interface ProblemHandling extends
          GeneralAdviceTrait,
          HttpAdviceTrait,
          IOAdviceTrait,
          RoutingAdviceTrait,
          SecurityAdviceTrait,
          ValidationAdviceTrait {
  
  }
  ```

* ```java
  public interface ProblemAdviceTrait extends AdviceTrait {
  
      @API(status = INTERNAL)
      @ExceptionHandler
      default ResponseEntity<Problem> handleProblem(
              final ThrowableProblem problem,
              final NativeWebRequest request) {
          return create(problem, request);
      }
  
  }
  ```

* 

