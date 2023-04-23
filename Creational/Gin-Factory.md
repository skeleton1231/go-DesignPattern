# Gin工厂模式

Gin使用了工厂模式来创建HTTP路由器以及HTTP处理器。让我们具体看看Gin中是如何使用工厂模式的

### 1.创建HTTP路由器实例

在Gin中，HTTP路由器是通过工厂函数gin.Default()或gin.New()创建的。这两个工厂函数分别返回具有默认中间件和没有任何中间件的Gin引擎实例。这种方式确保了在创建HTTP路由器时，引擎实例已经具备了必要的设置。

### 2.注册HTTP处理器

Gin框架允许你为HTTP路由定义处理器。处理器可以看作是一个工厂，它可以根据请求的上下文生成HTTP响应。在Gin中，你可以使用r.GET()、r.POST()等方法为特定路由注册处理器。

```go
r.GET("/ping", func(c *gin.Context) {
    c.JSON(200, gin.H{
        "message": "pong",
    })
})
```

在这个例子中，我们为/ping路由注册了一个匿名函数作为处理器。当客户端发送GET请求到/ping时，处理器会根据请求的上下文生成JSON响应。这里的处理器就相当于一个简单的工厂，根据请求上下文创建不同的响应。

### 3.中间件

Gin框架还支持使用中间件。中间件可以看作是处理器的工厂，它可以在处理器执行前后执行一些操作，如日志记录、权限验证等。你可以通过r.Use()方法为Gin引擎添加中间件。

```go
r.Use(gin.Logger())
r.Use(gin.Recovery())
```
在这个例子中，我们为Gin引擎添加了两个中间件：gin.Logger()和gin.Recovery()。这两个中间件分别负责日志记录和恢复Panic。它们都是通过工厂函数创建的，这些工厂函数返回了实现了特定功能的中间件实例。