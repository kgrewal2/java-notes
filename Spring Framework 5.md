

> Date: June 29, 2021

# IoC Container

IoC - Inversion of Control
Also known as dependency injection.

Objects define their dependencies and the container injects those dependencies when it creates the bean.

The bean itself should not control the instantiation/location of its dependencies.

## Configuration

The configuration files give the instructions to Spring IoC Container on how to instantiate, configure and assemble the beans.

Spring supports XML, Annotations, Groovy DSL, and Java based Configurations.

## Instantiating a Container

```java
// XML Based
ApplicationContext context = new ClassPathXmlApplicationContext(
    "services.xml", "daos.xml"
)
```

```java
// Groovy Based
Application context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```

```java
// Generic Option
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml");
context.refresh();
```

## Using Container

`ApplicationContext` lets you read bean definitions and access them.
Method used: `T getBean(String name, Class<T> requiredType)`.

```java
Application context = new ClassPathXmlApplicationContext(...);

PetStoreService service = context.getBean("petStore", PetStoreService.class);
```

## Beans

Objects that form the backbone of the applications and managed by the Spring IoC Container.

Bean Definitions are represented as `BeanDefinition` objects, which contain the following metadata:

1. Package Qualified Class Name.
2. Behavioural Configuration Elements such as scope, callbacks...
3. References to other beans to do the work. These references are called collaborators or dependencies.
4. Other configuration settings for a newly created object. Example: Number of connections in bean that manages connection pool.

`ApplicationContext` contains information about creating a specific bean and also permits the registratino of existing objects that are created outside the container.

This permit process is done by access the BeanFactory through `getBeanFactory()` method of `ApplicationContext`, which returns the `DefaultListableBeanFactory` implementation. Then registration is done using `registerSingleton(...)` or `registerBeanDefinition()`.

### Naming Beans

Beans can be named by using the `id` attribute and multiple `name` attributes. Multiple names are allowed so that different components of the application can access the bean through names specific to that component.

```xml
// Aliasing bean name
<alias name="fromName" alias="toName"/>
```

### Instantiation with a Constructor

Simply specifying the bean class in this case is sufficient. You may need a default (empty) constructor.

```xml
<bean id="beanName" class="examples.BeanName" />
```

### Instantiation with a Static Factory Method

A static method to create instance is required in this case and is configured as `factory-method`.

```xml
<bean id="clientService"
    class="examples.ClientService"
    factory-method="createInstance"/>
```

```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    public static ClientService createInstance() {
        return clientService;
    }
}
```

### Instantiation by using an Instance Factory Method

This approach invokes a non-static method of an existing bean from the container to create a new bean. To use this mechanism, the `class` attribute is left empty and `factory-bean` attribute contains the name of a bean in current container instance method to create the object. The `factory-method` attribute contains the name of the factory method.

```xml
// This is the factory bean which contains the "createInstance" method.
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    // Inject any dependencies required by this locator bean.
</bean>

<!--
   Beans to be created via factory bean
-->
<bean id="clientService"
      factory-bean="serviceLocator"
      factory-method="createClientServiceInstance">
</bean>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```

```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();
    
    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
    
    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
```

## Dependencies

### Dependency Injection

Process whereby objects define their dependencies instead of instantiating the dependencies on their own. The container of the objects has the responsibility of providing the dependencies. This is why the process is called Inversion of Control as the Container implies all the control.

- Cleaner Code
- Decoupled Code

### Constructor based DI

Here the arguments of the constructor represent a dependency. The arguments to constructor and `static` factory method are treated equally.

```java
public class SimpleMovieLister {
    private final MovieFinder movieFinder;
    
    // movieFinder is injected by the container
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

#### Argument Resolution

```java
public class ExampleBean {

    private final int years;
    private final String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

##### Type/Value

```xml
// Giving types/values for constructor arguments
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

##### Index

```xml
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```

##### Name

```java
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```



Note: To make this work, `debug` flag should be enabled. Otherwise `@ConstructorProperties` annotation can be used.

```java
@ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
```

### Setter based DI

```java
public class SimpleMovieLister {
    private MovieFinder movieFinder;

    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

### Constructor based versus Setter based DI

Constructor DI -> Mandatory Dependencies
Setter DI -> Optional Dependencies

`@Required` annotation can be used to mandate setter based DI.

Constructor based DI -> Recommended because it lets you create immutable objects. It also ensures that required dependencies are not `null`.

### `idref `Element

```xml
// Original XML
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```

```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        // Using idref
        <idref bean="theTargetBean"/>
    </property>
</bean>
```

`idref` is preferred because it validate the bean name typo at deployment as opposed to the original way in which no validation is done.

### Empty values in XML

```xml
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```

This is equivalent to:

```java
exampleBean.setEmail("");
```

### Null values in XML

```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```

This is equivalent to:

```java
exampleBean.setEmail(null);
```

### Forcing Dependency Initialization using `depends-on`

```xml
<bean id="beanOne" class="ExampleBean" depends-in="manager,accountDao">
    <property name="manager" ref="manager" />
</bean>
```

`depends-on` explitly forces `manager` and `accountDao` initialization before `beanOne` is initialized.

`depends-on` can also be used to control shutdown order.

## Lazy Initialization

Lazy initialization of a bean can be configured using `lazy-init` attribute. Such a bean will be created only when requested, rather than start-up.

```xml
<bean id="lazy" class="com.something.ExpensiveToCreateBean" lazy-init="true"/>
```

Lazy initialization can also be done on a container level.

```xml
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```

## Autowiring Collaborators

Autowiring enables you to update the configuration of bean creation without needing to modify the configuration. Explicit wiring can be done once the code base becomes stable.

### Modes

- `no` (Default)
  No autowiring and bean references are defined by `ref` elements.
- `byName`
  Autowiring by property name.
- `byType`
  Autowires if exactly one bean of that property type exists in the container. Otherwise a fatal exception is thrown. If there are no matching beans, the property is not set.
- `constructor`
  Same as `byType` but applies to constructor arguments.

> Autowiring is good only if done consistently across the project. It should not be done for one or two bean definitions.

A particular bean can be set as the primary candidate using the `primary` attribute of its `<bean>` element to `true`.

A bean can be excluded from autowiring by setting `autowire-candidate` of that bean element to `false`.

------

> Date: June 30, 2021

## Method Injection

Suppose a bean A requires bean B on its method invocations and the life cycle of bean B is different. The container gets only one opportunity to set the properties. The container cannot provide bean A with a new instance of bean B every time one is needed. 

The solution is to forego some inversion of control. You can make bean A aware of the container using `ApplicationContextAware` interface, and making a `getBean("B")` call to the container every time bean A requires the instance of bean B.

```java
public class CommandManager implements ApplicationContextAware {
    private ApplicationContext applicationContext;
    
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
    
    protected Command fetchCommandBean() {
        return this.applicationContext.getBean("command", Command.class);
    }   
}
```

### Lookup Method Injection

Its the ability of the container to override methods on container-managed beans and return the lookup result for another named bean in the container. 

Container dynamically overrides the implementation of the `fetchCommandBean()` method. So, the method should be abstract and ideally in this form.

```java
<public|protected> [abstract] <return-type> theMethodName(no-arguments);
```

Code below shows the code of Command Manager being dynamically implemented by the container to fetch the `Command` bean.

```java
public abstract class CommandManager {
    public Object process(Object commandState) {
        Command command = fetchCommandBean();
        command.setState(commandState);
        return command.execute();
    }
    
    protected abstract Command fetchCommandBean();
}
```

#### XML Based Lookup Method Injection

```java
<bean id="myCommand" class="fiona.apple.AsyncCommand" scope="prototype">
    <!-- inject dependencies here -->
</bean>
    
<bean id="commandManager" class="fiona.apple.CommandManager">
    <lookup-method name="fetchCommandBean" bean="myCommand"/>
</bean>
```

#### Annotations Based Lookup Method Injection

```java
public abstract class CommandManager {

    public Object process(Object commandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
    
    // OR
    
    @Lookup
    protected abstract MyCommand createCommand();
}
```

## Bean Scopes

Bean definition is like creating a recipe for creating an actual instance of the class. One can create many object instances from a single recipe.

The scope of the objects created from a particular bean can be configured.

### Bean Scopes - Classification

1. Singleton (Default)
   Single Object Instance --> Each Spring IoC Container.
   Stored in the cache of all singleton beans.
2. Prototype
   Any number of object instances.
   Creation of new bean instance every time a request for that specific bean is made.
   The destruction lifecycle of the Prototype objects should be done by the client and not Spring Container. See [`bean post-processor`](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-extension-bpp).
3. Request
   Lifecycle of a single HTTP Request.
   Annotation: `@RequestScope`
4. Session
   Lifecycle of an HTTP `Session`.
   Annotation: `@SessionScope`
5. Application
   Lifecycle of a `ServletContext`.
   Annotation: `@ApplicationScope`
6. Websocket
   Lifecycle of a `WebSocket`

Scope 3, 4, 5, and 6 are valid only in the context of web-aware Spring `ApplicationContext`.

> Stateful --> Prototype Scope
> Stateless --> Singleton Scope

### Scoped Beans as Dependencies

When a bean of shorted scope is a dependency for the longer scope bean, AOP proxy is used. It exposes the same interface and can delegate the method calls onto the real object.

### Custom Scope

#### Registering Scope

- `registerScope(String scopeName, Scope scope)`
  `scopeName`: unique name associated with a scope.
  `scope`: custom implementation that is to be registered and used.

```java
Scope threadScope = new SimpleThreadScope();
beanFactory.registerScope("thread", threadScope);
```

#### Writing Custom Scope

Four methods need to be implemented in order to create a new scope.

```java
Object get(String name, ObjectFactory<?> objectFactory);
```

```java
Object remove(String name);
```

```java
void registerDestructionCallback(String name, Runnable destructionCallback);
```

```java
String getConversationId()
```

## Customizing the nature of a bean

### Lifecycle Callbacks

One can implement `InitializingBean` and `DisposableBean` interfaces to interact with the bean lifecycle management. The container calls `afterPropertiesSet()` (InitializingBean) for the initialization and `destroy()` (DisposableBean) for the destruction of your beans.

#### Initialization Callbacks

`InitializingBean` interface lets a bean perform initialization work after the container has set all necessary properties on the bean.

It is recommended to use `@PostConstruct` instead of `InitializingBean` interface to avoid code coupling with Spring.

##### `InitializationBean` implementation

```xml
<bean id="exampleBean" class="ExampleBean" />
```

```java
public class ExampleBean implements InitializationBean {
    @Override
    public void afterPropertiesSet() {
        // do some work
    }
}
```

##### XML Based Initialization Callback

```xml
<bean id="exampleBean" class="ExampleBean" init-method="initMethod"/>
```

```java
public class ExampleBean {
    public void init() {
        // do some work
    }
}
```

#### Destruction Callbacks

It is recommended to use `@PreDestroy` instead of `DisposableBean` interface to avoid code coupling with Spring.

##### XML Based Destruction Callbacks

```xml
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```

```java
public class ExampleBean {
    public void cleanup() {
        // do some destruction work
    }
}
```

##### `DisposableBean` implementation

```xml
<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
```

```java
public class AnotherExampleBean implements DisposableBean {
    @Override
    public void destroy() {
        // do some destruction work
    }
}
```

#### Default Initialization and Destroy Methods

```java
<beans default-init-methods="init">
    <!-- beans -->
</beans>
```

This XML configuration can be used to have a consistent naming for initialization and destruction callback methods throughout the project without mentioning the `init-method` for each bean.

#### Startup and Shutdown Callbacks

```java
public interface Lifecycle {
    void start();
    void stop();
    boolean isRunning();
}
```

```java
public interface LifecycleProcessor extends Lifecycle {
    void onRefresh();
    void onClose();
}
```

Both these interfaces can be used to configure the start and shutdown callbacks of a bean.

`SmartLifeCycle` can be used to get more finely grained control over the auto-startup of a specific bean.

#### Shutting down the Spring IoC Container Gracefully in Non-Web Applications

This applies to non-web applications because the Spring web-based `ApplicationContext` implementations already have a code in place to gracefully shut down the Spring IoC container.

##### Registering shutdown hook

```java
public final class Boot {
    public static void main(final String[] args) throws Exception {
        ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");
        // hook registered for above context
        ctx.registerShutdownHook();
        
        // app runs here
        
        // main method exits, hook is called prior to the app shutting down
    }
}
```

## Annotation Based Container Configuration

Annotation based configuration mixes up the configuration and code. Thus, not preferred by some developers.

### `@Required`

This annotations implies that the affect bean property must be populated at the configuration time.
It allows for eager and explicity failure avoiding `NullPointerException` instances or the like later on.

```java
private MovieFinder movieFinder;

@Required
public void setMovieFinder(MovieFinder movieFinder) {
    this.movieFinder = movieFinder;
}
```

### `@Autowired`

#### Autowiring Constructor

```java
public class MovieRecommender {
    private final CustomerPreference customerPreference;
    
    @Autowired
    public MovieRecommender(CustomerPreference preference) {
        this.customerPreference = preference;
    }
}
```

#### Autowiring Setter

```java
public class MovieRecommender {
    private CustomerPreference customerPreference;
    @Autowired
    public void setCustomerPreference (CustomerPreference customerPreference) {
        this.customerPreference = customerPreference;
    }
}
```

#### Autowiring a method

```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;
    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }
}
```

#### Autowiring fields

```java
@Autowired
private MovieCatalog movieCatalog;
```

This can be mixed with constructors in which one field is set by autowiring the field and another one with the constructor.

#### Autowiring to get all beans of a particular type

```java
public class MovieRecommender {
    @Autowired
    private MovieCatalog[] movieCatalog;
    ...
}
```

This will put all beans of type `MovieCatalog` in `movieCatalog` variable.

Same can be done for typed collections like `Set<MovieCatalog> movieCatalog`.

Typed `Map` instances can be autowired as long as the expected key type is `String`. (`Map<String, MovieCatalog>`).

### `@Primary`

Autowiring by type may lead to multiple candidates, it is often necessary to have more control over the selection process.
A particular bean should be given preference using `@Primary`.

```java
@Configuration
public class MovieConfiguration {
    @Bean
    @Primary
    public MovieCatalog firstMovieCatalog() { ... }
    
    @Bean
    public MovieCatalog secondMovieCatalog() { ... }
}
```

```java
public class MovieRecommender {
    @Autowired
    private MovieCatalog movieCatalog;
    ...
}
```

### Qualifiers

`@Qualifier` can be used to provide more control over the selection process.

```java
@Autowired
@Qualifier("main")
private MovieCatalog movieCatalog;
```

```java
@Autowired
public void prepare(@Qualifier("main") MovieCatalog movieCatalog) {
    this.movieCatalog = movieCatalog;
}
```

#### Custom Qualifier

```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Genre {
    String value();
}
```

```java
public class MovieRecommender {
    @Autowired
    @Genre("Action")
    private MovieCatalog actionCatalog;
}
```

### `@Resource`

```java
@Resource(name="myMovieFinder")
public void setMovieFinder(MovieFinder movieFinder) {
    this.movieFinder = movieFinder;
}
```

`@Resource` takes a name attribute and spring interprets that value as the bean name to be injected. It kind of follows by-name semantics.

If no name is explicitly specified, the default name is derived from the field name or the setter method.

### `@Value`

This is used to inject externalized properties.

```java
@Component
public class MovieRecommender {
    private final String catalog;
    
    public MovieRecommender(@Value("${catalog.name}") String catalog) {
        this.catalog = catalog;
    }
}
```

```java
@Configuration
@PropertySource("classpath:application.properties")
public class AppConfig { }
```

```xml
// application.properties

catalog.name=MovieCatalog
```

A default value can be set as:

```java
@Value("${catalog.name:defaultCatalog}")
```

### `@PostConstruct` and `@PreDestroy`

```java
public class MovieCache {
    @PostConstruct
    public void populateMovieCache() {
        // populate the movie cache upon initialization
    }
    @PreDestroy
    public void clearMovieCache(){
        // clear the cache upon destruction
    }
}
```

## Classpath Scanning and Managed Components

`@Repository` is a marker for any class that fulfills the role or stereotype of a repository (also known as Data Access Object or DAO). `@Component` is a generic stereotype for Spring-managed component. 

`@Repository`, `@Service` and `Controller` are specializations of `@Component` for more specific use cases (persistence, service, and presentation respectively).

### Composing Annotations

Multiple meta-annotations can be used to create "composed annotations". Example: `@RestController` annotation from Spring MVC is composed of `@Controller` and `@ResponseBody`.

### Auto-detection of classes and registering bean definitions

```java
@Service
public class SimpleMovieLister {
    ...
}
```

```java
@Repository
public class JpaMovieFinder implements MovieFinder {
    ...
}
```

To autodetect these classes and register to corresponding beans, we need to add `@ComponentScan` to the `@Configuration` class where `basePackages` attribute is a common parent package for the two classes. (comma- or semicolon- or space-separated list that includes the parent package can also be used.)

```java
@Configuration
@ComponentScan(basePackages="org.example")
public class ApplicationConfig {
    ...
}
```

## Java Based Configuration

`@Bean` means that the method instantiates, configures and initializes a new object to be managed by the Spring IoC container.

### Initiatiating the Spring Container using `AnnotationConfigApplicationContext`

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class, ServiceImplementation.class);

context.getBean(MyService.class);
```

Web Application Support is given by `AnnotationConfigWebApplicationContext`

### `@Bean`

```java
@Configuration
public class AppConfig {
    
    @Bean(name = "myThing")
    // OR
    @Bean({"myThing", "aliasName", "aliasName2"})
    @Description("Provides a basic thing")
    public Thing thing() {
        return new Thing();
    }
}
```

### Composing Configurations

```java
@Configuration
public class ConfigA {

    @Bean
    public A a() {
        return new A();
    }
}

@Configuration
@Import(ConfigA.class)
public class ConfigB {

    @Bean
    public B b() {
        return new B();
    }
}
```

```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(ConfigB.class);

    // now both beans A and B will be available...
    A a = ctx.getBean(A.class);
    B b = ctx.getBean(B.class);
}
```

## Profiles

### `@Profile`

```java
@Configuration
@Profile("development")
public class StandaloneDataConfig {
    ...
}
```

```java
@Configuration
@Profile("production")
public class JndiDataConfig {
    ...
}
```

### Activating Profile

```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh();
```

### Default Profile

```java
@Profile("default")
// Used if no profile is active
```
