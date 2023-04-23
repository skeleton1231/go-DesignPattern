在 Gin 中，选项模式可以用于自定义中间件或其他组件的行为。以下是一个使用选项模式创建自定义日志中间件的例子：

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
	"time"
)

// LoggingMiddlewareConfig 是日志中间件的配置结构体
type LoggingMiddlewareConfig struct {
	// 是否记录日志
	Enabled bool
	// 是否记录请求体
	LogRequestBody bool
}

// 默认配置
var defaultLoggingMiddlewareConfig = LoggingMiddlewareConfig{
	Enabled:        true,
	LogRequestBody: false,
}

// LoggingMiddlewareOption 定义选项接口
type LoggingMiddlewareOption interface {
	apply(*LoggingMiddlewareConfig)
}

type loggingMiddlewareOptionFunc func(*LoggingMiddlewareConfig)

func (f loggingMiddlewareOptionFunc) apply(c *LoggingMiddlewareConfig) {
	f(c)
}

// WithEnabled 设置是否启用日志记录
func WithEnabled(enabled bool) LoggingMiddlewareOption {
	return loggingMiddlewareOptionFunc(func(c *LoggingMiddlewareConfig) {
		c.Enabled = enabled
	})
}

// WithLogRequestBody 设置是否记录请求体
func WithLogRequestBody(logRequestBody bool) LoggingMiddlewareOption {
	return loggingMiddlewareOptionFunc(func(c *LoggingMiddlewareConfig) {
		c.LogRequestBody = logRequestBody
	})
}

// NewLoggingMiddleware 使用选项模式创建自定义日志中间件
func NewLoggingMiddleware(opts ...LoggingMiddlewareOption) gin.HandlerFunc {
	config := defaultLoggingMiddlewareConfig

	for _, opt := range opts {
		opt.apply(&config)
	}

	return func(c *gin.Context) {
		if !config.Enabled {
			c.Next()
			return
		}

		start := time.Now()

		// 将请求传递给下一个处理程序
		c.Next()

		// 请求处理完成后，计算处理时间
		elapsed := time.Since(start)
		fmt.Printf("Request took %s\n", elapsed)

		if config.LogRequestBody {
			fmt.Printf("Request body: %s\n", c.Request.Body)
		}
	}
}

func main() {
	router := gin.Default()

	// 使用选项模式创建自定义日志中间件
	loggingMiddleware := NewLoggingMiddleware(
		WithEnabled(true),
		WithLogRequestBody(true),
	)

	// 使用自定义日志中间件
	router.Use(loggingMiddleware)

	router.GET("/", func(c *gin.Context) {
		c.String(http.StatusOK, "Hello, Gin!")
	})

	router.Run(":8080")
}
```

在这个例子中，我们创建了一个名为 LoggingMiddlewareConfig 的结构体来存储中间件的配置。我们还定义了一个选项接口 LoggingMiddlewareOption，并为每个选项实现一个函数，这些函数会修改 LoggingMiddlewareConfig 的值。

然后，我们创建了一个名为 NewLoggingMiddleware 的函数，它接受一系列选项并返回一个自定义的日志中间件。在这个函数中，我们将选项应用于默认配置，并根据最终配置创建中间件。

最后，我们在主函数中使用 NewLoggingMiddleware 创建自定义日志中间件，并将其添加到 Gin 路由器中。这样，我们可以通过传递选项来定制日志中间件的行为。