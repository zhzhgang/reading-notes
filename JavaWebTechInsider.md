# 第九章 Servlet 工作原理解析


## 9.1 Servlet 容器

Servlet 与 Servlet 容器彼此依存又独立发展，通过标准化接口相互协作。

以 Tomcat 为例，Tomcat 容器分为 4 个 等级，真正管理 Servlet 的容器是 Context 容器，Context 容器直接管理 Servlet 在容器中的包装类 Wrapper，一个 Context 对应一个 Web 工程。

### 9.1.1 Servlet 容器的启动过程


