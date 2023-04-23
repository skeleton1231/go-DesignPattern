# 策略模式（Strategy Pattern）

策略模式（Strategy Pattern）是一种行为型设计模式，它定义了一系列算法，并将每个算法封装在一个具有共同接口的类中，使得这些算法可以相互替换。策略模式让算法独立于使用它的客户端。

在Gin框架中，我们可以看到策略模式的影子。例如，Gin框架允许用户自定义中间件，不同的中间件可以根据业务需求灵活地切换和组合。

首先，让我们定义一个策略接口，用于表示不同的中间件策略：

```go
package middleware

import "github.com/gin-gonic/gin"

type MiddlewareStrategy interface {
	Apply(*gin.Context)
}
```

接下来，我们创建两个实现了MiddlewareStrategy接口的中间件策略：

### 1.日志中间件策略：

```go
package middleware

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"time"
)

type LoggingMiddleware struct{}

func (lm *LoggingMiddleware) Apply(c *gin.Context) {
	startTime := time.Now()
	c.Next()
	endTime := time.Now()

	fmt.Printf("Request processed in %v ms\n", endTime.Sub(startTime).Milliseconds())
}
```

### 2.认证中间件策略：

```go
package middleware

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type AuthMiddleware struct{}

func (am *AuthMiddleware) Apply(c *gin.Context) {
	token := c.Request.Header.Get("Authorization")
	if token != "my-secret-token" {
		c.AbortWithStatusJSON(http.StatusUnauthorized, gin.H{"error": "Unauthorized"})
		return
	}
	c.Next()
}
```

在Gin框架中，我们可以通过如下方式将这两个中间件策略应用到路由上：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"your_project/middleware"
)

func main() {
	r := gin.Default()

	loggingMiddleware := &middleware.LoggingMiddleware{}
	authMiddleware := &middleware.AuthMiddleware{}

	r.Use(func(c *gin.Context) {
		loggingMiddleware.Apply(c)
	})

	r.Use(func(c *gin.Context) {
		authMiddleware.Apply(c)
	})

	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})

	r.Run(":8080")
}
```

在这个示例中，我们首先创建了LoggingMiddleware和AuthMiddleware实例，然后通过r.Use()方法将它们应用到所有路由上。这样，我们可以灵活地使用不同的中间件策略来满足不同的业务需求。

策略模式的优点包括：提高代码的复用性、易于扩展和维护、实现了算法和客户端的解耦。在实际项目中，当你需要在不同的场景下使用不同的算法或策略时，可以考虑使用策略模式。