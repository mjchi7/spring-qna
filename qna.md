# Spring Framework - QnA
### What are the different type of injection available?
Dependency injection can be done through a) constructor injection b) setter injection c) field injection

### Why are field injections frowned upon?
Some of the reason why people might find field injection bad:
1. Implicit dependency.  
    With field injection, the dependency is implicit and the creator will not be able to figure out the dependency from any public interface.
    Compared to constructor injection, whereby dependencies are explicitly stated in the constructor arguments.
    Besides that, whenever dependency changes, IDE easily raise issue on tests that allow developer to fix it, instead of failing silently due to NPE.
2. Object might be in an undefined state  
    Field injections are more prone to NPE, in the event beans are not injected. However, compared to constuctor injection, we can add several assert.NotNull in the constructor, making sure the beans are valid before creating the object.
3. Immutability  
     Dependency set through constructor can be made immutable, whereas the dependency injected through field cannot be made final. One implication with making dependency immutable is that greatly reduces circular dependency problem.
4. DI Container Coupling  
     Having dependency injected through field injection means we'll always need either a DI container (e.g. start up spring container), or reflection magic (e.g. mockito @InjectMock). If we however do it through constructor injection, for simple test we can simply omit both and create the class under test using `new`.

See also: [link](https://stackoverflow.com/a/39892204/9897617)

### "Whenever you use a field injection, a unit test die" - Josh Long
It was mentioned in the talk [here](https://www.youtube.com/watch?v=sbPSjI4tt10&feature=youtu.be&t=29m22s&ab_channel=InfoQ)

After reading through the [article here](https://tedvinke.wordpress.com/2014/02/13/mockito-why-you-should-not-use-injectmocks-annotation-to-autowire-fields/), here's my interpretation:

Facts:
1. Field injections are implicit dependencies. Meaning that if we inject by field, there's no way for caller to know about that unless they look at the components' code. Contrast to constructor dependency whereby compiler will fail because of argument mismatch
2. The annotation @InjectMocks will fail silently if dependencies are not found. 

Imagine the scenario: A developer add a new dependency to a service. It compiles alright, but tests are failing, due to NPE. What if constructor injectors are used instead? Compiles fail because of argument mismatch when it is trying to create the server in test. Immediate action can be taken

### What about setter injection?
Most useful if a re-injection of certain dependencies are needed. Downside is there are a good amount of boilerplate code to be created.
See also: [Spring docs](https://docs.spring.io/spring-framework/docs/5.0.14.RELEASE/spring-framework-reference/core.html#beans-setter-injection)

### Rule of Thumbs About Dependency Injections?
1. For mandatory dependency, do it through **constructor injection**.
2. For optional dependency (or reinjectable), do it through **setter injection**

### Why Would an existing Spring project wants to migrate to Spring Boot?
Firstly, that can consolidate all the configurations to a Java based configuration. Then, having a spring boot dependency makes adding additional Spring component easy.   

For instance, imagine in the future, the application wants to add in Spring Actuator. Without Spring Boot, we'll need to configure it first before we can jump into the code. However with Spring Boot, default configuration has been supplied and only minimal configuration works need to be done.