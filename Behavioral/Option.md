# 选项模式（Option Pattern）

选项模式是一种设计模式，用于简化具有多个可选参数的对象或函数的初始化。选项模式允许我们以一种简洁、灵活的方式定义和传递可选参数。

在电商系统中，选项模式可以用于实现订单系统。当客户下订单时，订单可能有多个可选属性，例如优惠券、礼品包装等。使用选项模式，我们可以轻松地为订单添加或修改这些可选属性，而无需修改订单类或构造函数的签名。

以下是一个简单的订单选项模式示例：

```go
package main

type Order struct {
	ProductID    int
	Quantity     int
	DiscountCode string
	GiftWrap     bool
}

type OrderOption func(*Order)

func WithDiscountCode(discountCode string) OrderOption {
	return func(o *Order) {
		o.DiscountCode = discountCode
	}
}

func WithGiftWrap(giftWrap bool) OrderOption {
	return func(o *Order) {
		o.GiftWrap = giftWrap
	}
}

func NewOrder(productID int, quantity int, options ...OrderOption) *Order {
    order := &Order{
        ProductID: productID,
        Quantity: quantity,
    } 
    for _, option := range options {
	    option(order)
    }

    return order
}

func main() {
    order1 := NewOrder(1, 2)
    order2 := NewOrder(1, 2, WithDiscountCode("SAVE10"))
    order3 := NewOrder(1, 2, WithGiftWrap(true))
    order4 := NewOrder(1, 2, WithDiscountCode("SAVE20"), WithGiftWrap(true))
}
```

在这个例子中，我们定义了一个`Order`结构体，表示一个订单。订单有多个可选属性，例如`DiscountCode`（折扣代码）和`GiftWrap`（礼品包装）。我们为每个可选属性定义了一个选项函数，例如`WithDiscountCode`和`WithGiftWrap`。选项函数接受一个`Order`指针，并根据需要修改其属性。

`NewOrder`函数接受产品ID、数量和可变数量的选项函数作为参数。通过应用选项函数，我们可以根据需要为订单添加或修改可选属性。这使得订单的创建变得简洁而灵活。

### 优点：
- 选项模式提供了一种简洁、灵活的方式来处理具有多个可选参数的对象或函数的初始化。
- 使用选项模式，我们可以轻松地添加、修改或删除可选参数，而无需修改对象构造函数的签名。

### 缺点：
- 如果有大量的可选参数，选项模式可能会导致代码变得难以理解和维护。
- 选项模式可能会增加代码的复杂性，尤其是在处理多层嵌套选项时。

### 最后再给一个例子

```go
package options

import (
  "time"
)

type Connection struct {
  addr    string
  cache   bool
  timeout time.Duration
}

const (
  defaultTimeout = 10
  defaultCaching = false
)

type options struct {
  timeout time.Duration
  caching bool
}

// Option overrides behavior of Connect.
type Option interface {
  apply(*options)
}

type optionFunc func(*options)

func (f optionFunc) apply(o *options) {
  f(o)
}

func WithTimeout(t time.Duration) Option {
  return optionFunc(func(o *options) {
    o.timeout = t
  })
}

func WithCaching(cache bool) Option {
  return optionFunc(func(o *options) {
    o.caching = cache
  })
}

// Connect creates a connection.
func NewConnect(addr string, opts ...Option) (*Connection, error) {
  options := options{
    timeout: defaultTimeout,
    caching: defaultCaching,
  }

  for _, o := range opts {
    o.apply(&options)
  }

  return &Connection{
    addr:    addr,
    cache:   options.caching,
    timeout: options.timeout,
  }, nil
}

package main

import (
	"fmt"
	"options"
	"time"
)

func main() {
	// 使用默认选项创建连接
	conn1, _ := options.NewConnect("localhost:12345")
	fmt.Printf("Connection 1: %#v\n", conn1)

	// 使用自定义超时创建连接
	conn2, _ := options.NewConnect("localhost:12345", options.WithTimeout(5*time.Second))
	fmt.Printf("Connection 2: %#v\n", conn2)

	// 使用缓存创建连接
	conn3, _ := options.NewConnect("localhost:12345", options.WithCaching(true))
	fmt.Printf("Connection 3: %#v\n", conn3)

	// 使用自定义超时和缓存创建连接
	conn4, _ := options.NewConnect("localhost:12345", options.WithTimeout(5*time.Second), options.WithCaching(true))
	fmt.Printf("Connection 4: %#v\n", conn4)
}
```
