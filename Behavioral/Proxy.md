# 代理模式（Proxy Pattern）

代理模式是一种设计模式，其中一个对象（代理）扮演另一个对象（实际对象）的角色，并控制对实际对象的访问。代理模式可以用于实现安全控制、缓存、延迟初始化等功能。

在电商系统中，代理模式可以用于实现库存管理。例如，我们有一个库存服务，该服务处理库存查询和更新。为了提高性能和安全性，我们可以创建一个库存代理服务，用于拦截对库存服务的请求，并根据需要执行缓存、权限检查等操作。

```go
package main

type Inventory interface {
	GetProductStock(productID int) int
	UpdateProductStock(productID int, newStock int) error
}

type InventoryService struct {
	// ...
}

func (s *InventoryService) GetProductStock(productID int) int {
	// 查询库存的逻辑
}

func (s *InventoryService) UpdateProductStock(productID int, newStock int) error {
	// 更新库存的逻辑
}

type InventoryProxy struct {
	inventory  Inventory
	userRole   string
	stockCache map[int]int
}

func (p *InventoryProxy) GetProductStock(productID int) int {
	if stock, ok := p.stockCache[productID]; ok {
		return stock
	}
	stock := p.inventory.GetProductStock(productID)
	p.stockCache[productID] = stock
	return stock
}

func (p *InventoryProxy) UpdateProductStock(productID int, newStock int) error {
	if p.userRole != "admin" {
		return errors.New("权限不足")
	}
	delete(p.stockCache, productID)
	return p.inventory.UpdateProductStock(productID, newStock)
}
```