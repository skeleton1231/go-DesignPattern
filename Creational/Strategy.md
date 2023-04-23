# 策略模式 （Strategy Pattern）

策略模式的一个典型应用场景是在电商系统中计算不同类型的折扣策略。假设我们有三种折扣策略：无折扣（原价）、固定折扣（如95折）和VIP折扣。我们可以使用策略模式来实现这三种折扣策略。

首先，我们定义一个策略接口DiscountStrategy，用于表示不同的折扣策略：

```go
package discount

type DiscountStrategy interface {
	ApplyDiscount(amount float64) float64
}
```

接下来，我们创建三个实现了DiscountStrategy接口的折扣策略：

### 1.无折扣策略：

```go
package discount

type NoDiscount struct{}

func (nd *NoDiscount) ApplyDiscount(amount float64) float64 {
	return amount
}
```
### 2.固定折扣策略:

```go
package discount

type FixedDiscount struct {
	DiscountRate float64
}

func (fd *FixedDiscount) ApplyDiscount(amount float64) float64 {
	return amount * (1 - fd.DiscountRate)
}
```

### 3.VIP折扣策略:

```go
package discount

type VIPDiscount struct{}

func (vd *VIPDiscount) ApplyDiscount(amount float64) float64 {
	if amount >= 100 {
		return amount * 0.8
	}
	return amount
}
```

现在，我们创建一个上下文类Checkout，用于计算折扣后的价格：

```go
package discount

type Checkout struct {
	Strategy DiscountStrategy
}

func (c *Checkout) CalculatePrice(amount float64) float64 {
	return c.Strategy.ApplyDiscount(amount)
}
```

在实际业务中，我们可以根据不同的折扣策略来计算折扣后的价格：

```go
package main

import (
	"fmt"
	"your_project/discount"
)

func main() {
	noDiscount := &discount.NoDiscount{}
	fixedDiscount := &discount.FixedDiscount{DiscountRate: 0.05}
	vipDiscount := &discount.VIPDiscount{}

	checkout1 := &discount.Checkout{Strategy: noDiscount}
	checkout2 := &discount.Checkout{Strategy: fixedDiscount}
	checkout3 := &discount.Checkout{Strategy: vipDiscount}

	amount := 120.0
	fmt.Printf("No discount: %.2f\n", checkout1.CalculatePrice(amount))
	fmt.Printf("Fixed discount: %.2f\n", checkout2.CalculatePrice(amount))
	fmt.Printf("VIP discount: %.2f\n", checkout3.CalculatePrice(amount))
}
```

### 优点：

* 策略模式将算法的定义和使用分离，使得算法可以独立于使用它的客户端进行变化。
* 策略模式可以消除大量的条件判断语句，使得代码更简洁、易读和易维护。
* 通过使用策略模式，可以方便地扩展和添加新的策略，而无需修改已有的代码。
  
### 缺点：

* 客户端必须了解所有的策略，并根据不同情况选择合适的策略。这增加了客户端的复杂性。
* 当策略较多时，可能导致系统中存在许多策略类，增加系统的维护成本。

总结：

策略模式是一种行为型设计模式，它将算法封装到具有共同接口的独立的类中，使得它们可以互相替换。策略模式使得算法可以独立于使用它的客户端进行变化，从而提高代码的灵活性和可扩展性。

在本例子中，我们使用策略模式实现了一个简单的电商系统折扣计算功能。通过将不同的折扣策略抽象成接口，并提供不同的实现，我们可以轻松地为系统添加新的折扣策略，而无需修改现有的代码。同时，策略模式也有一定的缺点，如客户端需要了解所有的策略，以及可能导致较多策略类的存在。在实际项目中，我们需要根据项目需求和实际情况权衡策略模式的使用。