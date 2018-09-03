# 第九章 Servlet 工作原理解析


## 9.1 Servlet 容器

Servlet 与 Servlet 容器彼此依存又独立发展，通过标准化接口相互协作。

以 Tomcat 为例，Tomcat 容器分为 4 个 等级，真正管理 Servlet 的容器是 Context 容器，Context 容器直接管理 Servlet 在容器中的包装类 Wrapper，一个 Context 对应一个 Web 工程。

### 9.1.1 Servlet 容器的启动过程

Tomcat 的启动逻辑是基于观察者模式设计的，所有的容器继承 Lifecycle 接口，它管理容器的整个生命周期，所有容器的修改和状态的改变都会由它去通知已经注册的观察者。

当 Context 容器初始化状态设为 init 时，添加到 Context 容器的 Listener 将会被调用。ContextConfig 类继承了 LifecycleListener 接口，负责整个 Web 应用的配置文件的解析工作。

ContextConfig 的 init 方法完成后，Context 容器会执行 startInternal 方法。

### 9.1.2  Web 应用的初始化工作

Web 应用的初始化工作是在 ContextConfig 的 configureStart 方法中实现的，主要是解析 web.xml 文件。

web.xml 文件中的各个配置项将会被解析成相应的属性保存在 WebXml 对象中，接下来会将 WebXml 对象中的属性设置到 Context 容器中，包括创建 Servlet 对象、filter、listener 等。

Servlet 会被包装成 Context 容器中的 StandardWrapper。为什么要将 Servlet 包装成 StandardWrapper 而不是直接包装成 Servlet 对象，因为 StandardWrapper 是 Tomcat 容器中的一部分，具有容器的特征吗，而 Servlet 作为一个独立的 Web 开发标准，不应该强耦合在 Tomcat 中。

除了将 Servlet 包装成 StandardWrapper 并作为子容器添加到 Context 中外，web.xml 中所有其他属性都被解析到 Context 中，所以说 Context 容器才是真正运行 Servlet 的 Servlet 容器。一个 Web 应用对应一个 Context 容器，容器的配置属性由应用的 web.xml 指定。

## 9.2 创建 Servlet 实例


### 9.2.1 创建 Servlet 对象

如果 Servlet 的 load-on-startup 配置项对于 0，那么在 Context 容器启动时就会被实例化。

创建 Servlet 实例的方法是从 Wrapper.loadServlet 开始的。

### 9.2.2 初始化 Servlet

初始化 Servlet 在 StandardWrapper 的initServlet 中，这个方法调用 Servlet 的 init() 方法，同时把包装了 StandardWrapper 对象的StandardWrapperFacade 作为 ServletConfig 传给 Servlet

 