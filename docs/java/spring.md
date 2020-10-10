# Notes of Spring Framework

- Version: 5.2.8


## Bean


### 组件扫描

``` java
<context:component-scan/>
org.springframework.context.annotation.ComponentScanBeanDefinitionParser


@ComponentScan
public class AnnotationConfigApplicationContext extends GenericApplicationContext implements AnnotationConfigRegistry {

	private final AnnotatedBeanDefinitionReader reader;

	private final ClassPathBeanDefinitionScanner scanner;
...
}  
```

### Bean的生命周期


```
org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(String, RootBeanDefinition, Object[])
|- org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(String, RootBeanDefinition, Object[])
|-|- org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(String, RootBeanDefinition, Object[])

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(String, Object, RootBeanDefinition)
|- invokeAwareMethods
|- applyBeanPostProcessorsBeforeInitialization
|- invokeInitMethods
|- applyBeanPostProcessorsAfterInitialization

org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.destroyBean(Object)
```

### Aware: 感知回调接口

- `ApplicationContextAware`: 注入`ApplicationContext`.
- `ApplicationEventPublisherAware`: 注入事件发布者.
- `BeanClassLoaderAware`: 注入加载bean类的类加载器.
- `BeanNameAware`: 注入bean的名称.
- `BootstrapContextAware`: 注入容器运行的资源适配器`BootstrapContext`, 见JCA.
- `LoadTimeWeaverAware`: 注入加载时AspectJ编织器.
- `MessageSourceAware`: 注入解析消息的策略.
- `NotificationPublisherAware`: 注入Spring JMX通知发布者.
- `ResourceLoaderAware`: 注入访问资源的底层加载器.
- `ServletConfigAware`: 注入容器运行的`ServletConfig`.
- `ServletContextAware`: 注入容器运行的`ServletContext`.


#### 循环依赖


例:

```
->: 依赖于

A->B->C->A

```


``` java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {

	/** Maximum number of suppressed exceptions to preserve. */
	private static final int SUPPRESSED_EXCEPTIONS_LIMIT = 100;

  // 一级缓存: 完成初始化的单例对象
  // 缓存单例对象: bean名称 => bean实例
	/** Cache of singleton objects: bean name to bean instance. */
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

  // 三级缓存: 进入实例化阶段的单例对象工厂
  // 缓存单例对象的工厂: bean名称 => 对象工厂
	/** Cache of singleton factories: bean name to ObjectFactory. */
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

  // 二级缓存: 完成实例化阶段但未初始化, 提前暴露的单例对象
  // 缓存早期构造的单例对象: bean名称 => bean实例
	/** Cache of early singleton objects: bean name to bean instance. */
	private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);

	/** Set of registered singletons, containing the bean names in registration order. */
	private final Set<String> registeredSingletons = new LinkedHashSet<>(256);

	/** Names of beans that are currently in creation. */
	private final Set<String> singletonsCurrentlyInCreation =
			Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** Names of beans currently excluded from in creation checks. */
	private final Set<String> inCreationCheckExclusions =
			Collections.newSetFromMap(new ConcurrentHashMap<>(16));

	/** Collection of suppressed Exceptions, available for associating related causes. */
	@Nullable
	private Set<Exception> suppressedExceptions;

	/** Flag that indicates whether we're currently within destroySingletons. */
	private boolean singletonsCurrentlyInDestruction = false;

	/** Disposable bean instances: bean name to disposable instance. */
	private final Map<String, Object> disposableBeans = new LinkedHashMap<>();

	/** Map between containing bean names: bean name to Set of bean names that the bean contains. */
	private final Map<String, Set<String>> containedBeanMap = new ConcurrentHashMap<>(16);

	/** Map between dependent bean names: bean name to Set of dependent bean names. */
	private final Map<String, Set<String>> dependentBeanMap = new ConcurrentHashMap<>(64);

	/** Map between depending bean names: bean name to Set of bean names for the bean's dependencies. */
	private final Map<String, Set<String>> dependenciesForBeanMap = new ConcurrentHashMap<>(64);


  /**
	 * Return the (raw) singleton object registered under the given name.
	 * <p>Checks already instantiated singletons and also allows for an early
	 * reference to a currently created singleton (resolving a circular reference).
	 * @param beanName the name of the bean to look for
	 * @param allowEarlyReference whether early references should be created or not
	 * @return the registered singleton object, or {@code null} if none found
	 */
	@Nullable
	protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    // 尝试从一级缓存中获取单例对象
    Object singletonObject = this.singletonObjects.get(beanName);
    // 一级缓存中没找到, 且当前单例bean在创建中
		if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			synchronized (this.singletonObjects) {
        // 尝试从二级缓存中获取单例对象
				singletonObject = this.earlySingletonObjects.get(beanName);
        // 二级缓存中没找到, 且单例bean允许提前暴露
        if (singletonObject == null && allowEarlyReference) {
          // 尝试从三级缓存中创建单例对象工厂
					ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
					if (singletonFactory != null) {
						singletonObject = singletonFactory.getObject();
            // 放入二级缓存
						this.earlySingletonObjects.put(beanName, singletonObject);
            // 从三级缓存中移除
						this.singletonFactories.remove(beanName);
					}
				}
			}
		}
		return singletonObject;
	}

```

- constructor singleton: 构造对象时, 依赖的对象已在**当前创建bean池**中.
- setter singleton:
- prototype: 不缓存, 无法提前暴露创建中的bean.



### 自定义注解

#### Java元注解

`@Target`: 注解的作用目标

- `ElementType.TYPE`: 类型, 包括类/接口/枚举.
- `ElementType.FIELD`: 属性字段.
- `ElementType.METHOD`: 方法.
- `ElementType.PARAMETER`: 方法参数.
- `ElementType.CONSTRUCTOR`: 构造器.
- `ElementType.LOCAL_VARIABLE`: 本地局部变量.
- `ElementType.ANNOTATION_TYPE`: 注解.
- `ElementType.PACKAGE`: 包.


`@Retention`

- `RetentionPolicy.SOURCE`: 编译器可见, 不写入class文件中.
- `RetentionPolicy.CLASS`: 写入class文件中, 类加载阶段丢弃.
- `RetentionPolicy.RUNTIME`: 永久保存, 可以反射获取.


`@Documented`: 注解是否应该包含的JavaDoc中.


`@Inherited`: 是否允许子类继承该注解.



#### 实现自定义注解


- AOP加注解


```

package com.example;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyAnnotation {
    String value() default "";
    String key() default "";
}

@Aspect
public class AnnotationAspect {
  @Pointcut("@annotation(com.example.MyAnnotation)")
  public void anno() {}

  @Around("anno()")
  public void around(ProceedingJoinPoint joinPoint) {
    Object[] args = joinPoint.getArgs();
    MethodSignature sign = (MethodSignature) joinPoint.getSignature();

    MyAnnotation annotation =  sign.getMethod().getAnnotation(MyAnnotation.class);

    joinPoint.proceed();
    ...
  }
}
```


- Web应用中使用`HandlerInterceptor`获取`HandlerMethod`, 调用`HandlerMethod.getMethod()`获得`java.lang.reflect.Method`.

- 类上注解: 使用`BeanFactoryPostProcessor`和`ApplicationContextAware`, 在`BeanFactoryPostProcessor.postProcessBeanFactory(ConfigurableListableBeanFactory)`中使用自定义的`ClassPathBeanDefinitionScanner`自定义`GenericBeanDefinition`.


```
org.springframework.context.annotation.ClassPathBeanDefinitionScanner.ClassPathBeanDefinitionScanner(BeanDefinitionRegistry)

org.springframework.context.annotation.ClassPathScanningCandidateComponentProvider.setResourceLoader(ResourceLoader)

org.springframework.context.annotation.ClassPathScanningCandidateComponentProvider.registerDefaultFilters()

org.springframework.context.annotation.ClassPathBeanDefinitionScanner.scan(String...)
|- org.springframework.context.annotation.ClassPathBeanDefinitionScanner.doScan(String...) // 返回BeanDefinitionHolder

org.springframework.beans.factory.config.BeanDefinitionHolder.getBeanDefinition() //返回BeanDefinition

org.springframework.beans.factory.support.AbstractBeanDefinition.setBeanClass(Class<?>) // 自定义beanClass实现org.springframework.beans.factory.FactoryBean<T>
org.springframework.beans.factory.support.AbstractBeanDefinition.getPropertyValues()
org.springframework.beans.MutablePropertyValues.add(String, Object) // 设置属性传入org.springframework.beans.factory.support.AbstractBeanDefinition.getBeanClassName()获得类名称


```


``` java

```

### 事件监听


``` java
org.springframework.context
public abstract class ApplicationEvent extends java.util.EventObject

org.springframework.context.event
public @interface EventListener { ... }

org.springframework.context
public interface ApplicationListener<E extends ApplicationEvent> extends EventListener { ... }

```

内建事件:

``` java
java.util.EventObject
|- ApplicationEvent
|-|- ApplicationContextEvent
|-|-|- ContextRefreshedEvent        ConfigurableApplicationContext.refresh()调用时发布的事件: 所有bean已加载, post-processor bean被检测并激活, 单例被预实例化, ApplicationContext对象准备就绪. 在未关闭前可以多次创新(热刷新).
|-|-|- ContextStartedEvent          ConfigurableApplicationContext.start()调用时发布的事件: 所有Lifecycle bean收到显式开始信号.
|-|-|- ContextStoppedEvent          ConfigurableApplicationContext.stop()调用时发布的事件: 所有Lifecycle bean收到显式停止信号. 已停止的上下文可以用start()重新启动.
|-|-|- ContextClosedEvent           ConfigurableApplicationContext.close()调用或JVM关闭钩子时发布的事件: 所有单例bean将被销毁. 一旦上下文关闭, 不可再刷新或重启.
|-|- RequestHandledEvent            ApplicationContext中请求已处理事件.
|-|-|- ServletRequestHandledEvent   Servlet请求已被处理事件.
|-|- SpringApplicationEvent         SpringApplication相关的事件.
|-|- WebServerInitializedEvent      WebServer已准备号, 用于获取端口.
```

自定义事件:


``` java
public class BlockedListEvent extends ApplicationEvent { ... }
```

发布事件:


``` java
public class EmailService implements ApplicationEventPublisherAware {

  private List<String> blockedList;
  private ApplicationEventPublisher publisher;

  public void setApplicationEventPublisher(ApplicationEventPublisher publisher) {
    this.publisher = publisher;
  }

  public void sendEmail(String address, String content) {
    if (blockedList.contains(address)) {
      publisher.publishEvent(new BlockedListEvent(this, address, content));
      return;
    }
    // send email...
  }
}

```

``` java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver { ... }
```

接收事件:



``` java
public class BlockedListNotifier implements ApplicationListener<BlockedListEvent> {

  @Override
  public void onApplicationEvent(BlockedListEvent event) {
    ...
  }
}  
```

``` java
public class BlockedListNotifier {

  @EventListener
  public void processBlockedListEvent(BlockedListEvent event) {
    ...
  }
}  
```

## AOP


Spring AOP支持的AspectJ切点设计器(pointcut designator, PCD):

- `args`: 限制连接点匹配参数为指定类型的执行方法
- `@args`: 限制连接点匹配参数由指定注解标注的执行方法
- `execution`: 用于匹配是连接点的执行方法
- `this`: 限制连接点匹配AOP代理的bean引用为指定类型的类
- `target`: 限制连接点匹配目标对象为指定的类型的类
- `@target`: 限制连接点匹配特定的执行对象, 这些对象的类要具有指定类型的注解
- `within`: 限制连接点匹配指定的类型
- `@within`: 限制连接点匹配指定注解所标注的类型
- `@annotation`: 限制匹配带有指定注解的连接点


JDK动态代理, CGLIB动态代理的区别:

- JDK动态代理使用: 借助`Proxy`和`InvocationHandler`, 通过发射机制生成代理对象, 只能代理实现了接口的类.
- CGLIB动态代理: 使用asm修改代理对象类的字节码, 动态生成被代理类的子类, 并覆盖其方法.

## Web

### DispatcherServlet


```
DispatcherServlet
 |
 v
Servelet WebApplicationContext: Controllers, HandlerMapping, ViewResolver
 | delegate if no bean found
 v
Root WebApplicationContext: Services, Respositories
```

### Servelet容器初始化


``` java
public class SpringServletContainerInitializer implements javax.servlet.ServletContainerInitializer { // Spring的Servlet容器初始化器

  /**
	 * Delegate the {@code ServletContext} to any {@link WebApplicationInitializer}
	 * implementations present on the application classpath.
	 * <p>Because this class declares @{@code HandlesTypes(WebApplicationInitializer.class)},
	 * Servlet 3.0+ containers will automatically scan the classpath for implementations
	 * of Spring's {@code WebApplicationInitializer} interface and provide the set of all
	 * such types to the {@code webAppInitializerClasses} parameter of this method.
	 * <p>If no {@code WebApplicationInitializer} implementations are found on the classpath,
	 * this method is effectively a no-op. An INFO-level log message will be issued notifying
	 * the user that the {@code ServletContainerInitializer} has indeed been invoked but that
	 * no {@code WebApplicationInitializer} implementations were found.
	 * <p>Assuming that one or more {@code WebApplicationInitializer} types are detected,
	 * they will be instantiated (and <em>sorted</em> if the @{@link
	 * org.springframework.core.annotation.Order @Order} annotation is present or
	 * the {@link org.springframework.core.Ordered Ordered} interface has been
	 * implemented). Then the {@link WebApplicationInitializer#onStartup(ServletContext)}
	 * method will be invoked on each instance, delegating the {@code ServletContext} such
	 * that each instance may register and configure servlets such as Spring's
	 * {@code DispatcherServlet}, listeners such as Spring's {@code ContextLoaderListener},
	 * or any other Servlet API componentry such as filters.
	 * @param webAppInitializerClasses all implementations of
	 * {@link WebApplicationInitializer} found on the application classpath
	 * @param servletContext the servlet context to be initialized
	 * @see WebApplicationInitializer#onStartup(ServletContext)
	 * @see AnnotationAwareOrderComparator
	 */
	@Override
	public void onStartup(@Nullable Set<Class<?>> webAppInitializerClasses, ServletContext servletContext)
			throws ServletException { ... }
}
```

#### Spring在Servlet容器初始化中的操作


``` java
org.springframework.web.WebApplicationInitializer

public interface WebApplicationInitializer { // Web应用初始化器

	/**
	 * Configure the given {@link ServletContext} with any servlets, filters, listeners
	 * context-params and attributes necessary for initializing this web application. See
	 * examples {@linkplain WebApplicationInitializer above}.
	 * @param servletContext the {@code ServletContext} to initialize
	 * @throws ServletException if any call against the given {@code ServletContext}
	 * throws a {@code ServletException}
	 */
	void onStartup(ServletContext servletContext) throws ServletException;

}

// 实现

|- AbstractContextLoaderInitializer                         在Servlet上下文中注册ContextLoaderListener.
|-|- AbstractDispatcherServletInitializer                   在Servlet上下文中注册DispatcherServlet.
|-|-|- AbstractAnnotationConfigDispatcherServletInitializer 注册DispatcherServlet并使用基于Java的Spring配置.
|- AbstractReactiveWebInitializer
|- JerseyWebApplicationInitializer
|- SpringBootServletInitializer
```

``` java
public class org.springframework.web.context.ContextLoaderListener
  extends org.springframework.web.context.ContextLoader             // 上下文加载器
  implements javax.servlet.ServletContextListener                   // Servlet上下文监听器
  { ... }
```

初始化方法序列:

``` java
AbstractAnnotationConfigDispatcherServletInitializer

AbstractDispatcherServletInitializer.onStartup(ServletContext)
|- AbstractContextLoaderInitializer.onStartup(ServletContext)
|-|- AbstractContextLoaderInitializer.registerContextLoaderListener(ServletContext) // 在Servlet上下文中注册上下文加载监听器
|-|-|- AbstractAnnotationConfigDispatcherServletInitializer.createRootApplicationContext()
|-|-|- ContextLoaderListener.ContextLoaderListener(WebApplicationContext)
|-|-|- ContextLoader.setContextInitializers(ApplicationContextInitializer<?>...)
|-|-|- javax.servlet.ServletContext.addListener(ContextLoaderListener)

AbstractDispatcherServletInitializer.registerDispatcherServlet(ServletContext) // 在Servlet上下文中注册DispatcherServlet
|- AbstractDispatcherServletInitializer.createServletApplicationContext()
|-  AbstractDispatcherServletInitializer.createDispatcherServlet(WebApplicationContext)
|-|- DispatcherServlet.DispatcherServlet(WebApplicationContext) // 创建DispatcherServlet
|- FrameworkServlet.setContextInitializers(ApplicationContextInitializer<?>...)
|- javax.servlet.ServletContext.addServlet(String, Servlet) // 返回ServletRegistration.Dynamic
```



### 处理器中添加自定义方法参数


``` java
org.springframework.web.servlet.config.annotation.WebMvcConfigurer

  /**
   * Add resolvers to support custom controller method argument types.
   * <p>This does not override the built-in support for resolving handler
   * method arguments. To customize the built-in support for argument
   * resolution, configure {@link RequestMappingHandlerAdapter} directly.
   * @param resolvers initially an empty list
   */
  default void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
  }
```

``` java
/**
 * Strategy interface for resolving method parameters into argument values in
 * the context of a given request.
 *
 * @author Arjen Poutsma
 * @since 3.1
 * @see HandlerMethodReturnValueHandler
 */
public interface HandlerMethodArgumentResolver {

	/**
	 * Whether the given {@linkplain MethodParameter method parameter} is
	 * supported by this resolver.
	 * @param parameter the method parameter to check
	 * @return {@code true} if this resolver supports the supplied parameter;
	 * {@code false} otherwise
	 */
	boolean supportsParameter(MethodParameter parameter);

	/**
	 * Resolves a method parameter into an argument value from a given request.
	 * A {@link ModelAndViewContainer} provides access to the model for the
	 * request. A {@link WebDataBinderFactory} provides a way to create
	 * a {@link WebDataBinder} instance when needed for data binding and
	 * type conversion purposes.
	 * @param parameter the method parameter to resolve. This parameter must
	 * have previously been passed to {@link #supportsParameter} which must
	 * have returned {@code true}.
	 * @param mavContainer the ModelAndViewContainer for the current request
	 * @param webRequest the current request
	 * @param binderFactory a factory for creating {@link WebDataBinder} instances
	 * @return the resolved argument value, or {@code null} if not resolvable
	 * @throws Exception in case of errors with the preparation of argument values
	 */
	@Nullable
	Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
			NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception;

}
```

### 添加拦截器

``` java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LocaleChangeInterceptor());
        registry.addInterceptor(new
ThemeChangeInterceptor()).addPathPatterns("/**").excludePathPatterns("/admin/**");
        registry.addInterceptor(new
SecurityInterceptor()).addPathPatterns("/secure/*");
  }
}
```

``` java
/**
 * Workflow interface that allows for customized handler execution chains.
 * Applications can register any number of existing or custom interceptors
 * for certain groups of handlers, to add common preprocessing behavior
 * without needing to modify each handler implementation.
 *
 * <p>A HandlerInterceptor gets called before the appropriate HandlerAdapter
 * triggers the execution of the handler itself. This mechanism can be used
 * for a large field of preprocessing aspects, e.g. for authorization checks,
 * or common handler behavior like locale or theme changes. Its main purpose
 * is to allow for factoring out repetitive handler code.
 *
 * <p>In an asynchronous processing scenario, the handler may be executed in a
 * separate thread while the main thread exits without rendering or invoking the
 * {@code postHandle} and {@code afterCompletion} callbacks. When concurrent
 * handler execution completes, the request is dispatched back in order to
 * proceed with rendering the model and all methods of this contract are invoked
 * again. For further options and details see
 * {@code org.springframework.web.servlet.AsyncHandlerInterceptor}
 *
 * <p>Typically an interceptor chain is defined per HandlerMapping bean,
 * sharing its granularity. To be able to apply a certain interceptor chain
 * to a group of handlers, one needs to map the desired handlers via one
 * HandlerMapping bean. The interceptors themselves are defined as beans
 * in the application context, referenced by the mapping bean definition
 * via its "interceptors" property (in XML: a &lt;list&gt; of &lt;ref&gt;).
 *
 * <p>HandlerInterceptor is basically similar to a Servlet Filter, but in
 * contrast to the latter it just allows custom pre-processing with the option
 * of prohibiting the execution of the handler itself, and custom post-processing.
 * Filters are more powerful, for example they allow for exchanging the request
 * and response objects that are handed down the chain. Note that a filter
 * gets configured in web.xml, a HandlerInterceptor in the application context.
 *
 * <p>As a basic guideline, fine-grained handler-related preprocessing tasks are
 * candidates for HandlerInterceptor implementations, especially factored-out
 * common handler code and authorization checks. On the other hand, a Filter
 * is well-suited for request content and view content handling, like multipart
 * forms and GZIP compression. This typically shows when one needs to map the
 * filter to certain content types (e.g. images), or to all requests.
 *
 * @author Juergen Hoeller
 * @since 20.06.2003
 * @see HandlerExecutionChain#getInterceptors
 * @see org.springframework.web.servlet.handler.HandlerInterceptorAdapter
 * @see org.springframework.web.servlet.handler.AbstractHandlerMapping#setInterceptors
 * @see org.springframework.web.servlet.handler.UserRoleAuthorizationInterceptor
 * @see org.springframework.web.servlet.i18n.LocaleChangeInterceptor
 * @see org.springframework.web.servlet.theme.ThemeChangeInterceptor
 * @see javax.servlet.Filter
 */
public interface HandlerInterceptor {

	/**
	 * Intercept the execution of a handler. Called after HandlerMapping determined
	 * an appropriate handler object, but before HandlerAdapter invokes the handler.
	 * <p>DispatcherServlet processes a handler in an execution chain, consisting
	 * of any number of interceptors, with the handler itself at the end.
	 * With this method, each interceptor can decide to abort the execution chain,
	 * typically sending an HTTP error or writing a custom response.
	 * <p><strong>Note:</strong> special considerations apply for asynchronous
	 * request processing. For more details see
	 * {@link org.springframework.web.servlet.AsyncHandlerInterceptor}.
	 * <p>The default implementation returns {@code true}.
	 * @param request current HTTP request
	 * @param response current HTTP response
	 * @param handler chosen handler to execute, for type and/or instance evaluation
	 * @return {@code true} if the execution chain should proceed with the
	 * next interceptor or the handler itself. Else, DispatcherServlet assumes
	 * that this interceptor has already dealt with the response itself.
	 * @throws Exception in case of errors
	 */
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		return true;
	}

	/**
	 * Intercept the execution of a handler. Called after HandlerAdapter actually
	 * invoked the handler, but before the DispatcherServlet renders the view.
	 * Can expose additional model objects to the view via the given ModelAndView.
	 * <p>DispatcherServlet processes a handler in an execution chain, consisting
	 * of any number of interceptors, with the handler itself at the end.
	 * With this method, each interceptor can post-process an execution,
	 * getting applied in inverse order of the execution chain.
	 * <p><strong>Note:</strong> special considerations apply for asynchronous
	 * request processing. For more details see
	 * {@link org.springframework.web.servlet.AsyncHandlerInterceptor}.
	 * <p>The default implementation is empty.
	 * @param request current HTTP request
	 * @param response current HTTP response
	 * @param handler the handler (or {@link HandlerMethod}) that started asynchronous
	 * execution, for type and/or instance examination
	 * @param modelAndView the {@code ModelAndView} that the handler returned
	 * (can also be {@code null})
	 * @throws Exception in case of errors
	 */
	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable ModelAndView modelAndView) throws Exception {
	}

	/**
	 * Callback after completion of request processing, that is, after rendering
	 * the view. Will be called on any outcome of handler execution, thus allows
	 * for proper resource cleanup.
	 * <p>Note: Will only be called if this interceptor's {@code preHandle}
	 * method has successfully completed and returned {@code true}!
	 * <p>As with the {@code postHandle} method, the method will be invoked on each
	 * interceptor in the chain in reverse order, so the first interceptor will be
	 * the last to be invoked.
	 * <p><strong>Note:</strong> special considerations apply for asynchronous
	 * request processing. For more details see
	 * {@link org.springframework.web.servlet.AsyncHandlerInterceptor}.
	 * <p>The default implementation is empty.
	 * @param request current HTTP request
	 * @param response current HTTP response
	 * @param handler the handler (or {@link HandlerMethod}) that started asynchronous
	 * execution, for type and/or instance examination
	 * @param ex any exception thrown on handler execution, if any; this does not
	 * include exceptions that have been handled through an exception resolver
	 * @throws Exception in case of errors
	 */
	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable Exception ex) throws Exception {
	}

}
```

### 处理异常


请求映射或请求处理器中抛出异常时, `DispatcherServlet`委托`HandlerExceptionResolver` beand的链解析并处理异常.


``` java
org.springframework.web.servlet.DispatcherServlet.initHandlerExceptionResolvers(ApplicationContext


org.springframework.web.servlet.HandlerExceptionResolver
|- AbstractHandlerExceptionResolver
|-|- AbstractHandlerMethodExceptionResolver
|-|-|- ExceptionHandlerExceptionResolver      调用@Controller或@ControllerAdvice注解的类中`@ExceptionHandler`注解的方法来处理异常
|-|- DefaultHandlerExceptionResolver
|-|- ResponseStatusExceptionResolver          处理@ResponseStatus注解的异常, 映射到HTTP状态码
|-|- SimpleMappingExceptionResolver           异常类名称与错误视图名称的映射, 用于渲染错误页
|- DefaultErrorAttributes
|- HandlerExceptionResolverComposite

```


### ControllerAdvice


``` java
/**
 * Specialization of {@link Component @Component} for classes that declare
 * {@link ExceptionHandler @ExceptionHandler}, {@link InitBinder @InitBinder}, or
 * {@link ModelAttribute @ModelAttribute} methods to be shared across
 * multiple {@code @Controller} classes.
 ...
 */
public @interface ControllerAdvice { ... }


@ExceptionHandler   处理在特定处理器类和/或处理器方法中异常的注解.
@InitBinder         注解初始化WebDataBinder的方法, 绑定请求参数到模型对象.
@ModelAttribute     绑定方法参数活方法返回值命名的模型属性, 暴露给Web视图的注解.

```


## Data

### 事务

- 声明式事务: `@EnableTransactionManagement`, `@Transactional`, `TransactionInterceptor`, `TransactionManager`

- 编程式事务: `TransactionTemplate`, `TransactionOperator`, `TransactionManager`


声明式事务传播类型(`org.springframework.transaction.annotation.Propagation`):

- `REQUIRED`: 支持当前事务, 没有事务时创建新事务.
- `SUPPORTS`: 支持当前事务, 没有事务时按非事务方式执行.
- `MANDATORY`: 支持当前事务, 没有事务时抛出异常.
- `REQUIRES_NEW`: 创建新的事务, 如果当前有事务则将它挂起.
- `NOT_SUPPORTED`: 按非事务方式执行, 如果当前有事务则将它挂起.
- `NEVER`: 按非事务方式执行, 如果当前有事务抛出异常.
- `NESTED`: 如果当前有事务, 在嵌套事务中执行, 否则与`REQUIRED`一致.
