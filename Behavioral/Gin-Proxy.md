在 Gin 中，代理模式通常用于在处理 HTTP 请求时对特定操作进行封装和扩展。这可以通过中间件来实现。中间件可以用作代理，拦截和处理请求，然后将请求传递给下一个处理程序。这种方式使得在不修改原始处理程序的基础上，实现对功能的扩展。以下是一个使用代理模式的例子：

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
	"time"
)

// LoggingMiddleware 作为代理，记录请求的处理时间
func LoggingMiddleware(c *gin.Context) {
	start := time.Now()

	// 将请求传递给下一个处理程序
	c.Next()

	// 请求处理完成后，计算处理时间
	elapsed := time.Since(start)
	fmt.Printf("Request took %s\n", elapsed)
}

func main() {
	router := gin.Default()

	// 使用 LoggingMiddleware 中间件作为代理
	router.Use(LoggingMiddleware)

	router.GET("/", func(c *gin.Context) {
		c.String(http.StatusOK, "Hello, Gin!")
	})

	router.Run(":8080")
}
```

在这个例子中，我们定义了一个 LoggingMiddleware 函数，它充当代理。该代理在请求处理之前记录当前时间，并在请求处理完成后计算请求处理所花费的时间。LoggingMiddleware 使用 c.Next() 将请求传递给下一个处理程序。

然后我们在 Gin 路由器中使用 router.Use(LoggingMiddleware) 将这个中间件注册为代理。这样，所有请求都会经过这个代理。

当然，这只是一个简单的例子，您可以根据实际需求扩展代理的功能，例如实现身份验证、授权、缓存等。