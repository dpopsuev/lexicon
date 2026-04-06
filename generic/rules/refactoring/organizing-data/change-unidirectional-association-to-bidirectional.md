---
id: change-unidirectional-association-to-bidirectional
title: "Change Unidirectional Association to Bidirectional"
description: "Two classes need to reference each other but only one has a link."
labels: [refactoring, organizing-data]
---

# Change Unidirectional Association to Bidirectional

**Problem:** Two classes need to reference each other but only one has a link.

**Solution:** Add a back-reference and maintain both directions.

**Mechanics:**
1. Add back-reference field
2. Update setter to maintain both links
3. Decide which side owns the association

**Go example:**
```go
// Add back-reference
type Order struct { customer *Customer }
type Customer struct { orders []*Order }
func (o *Order) SetCustomer(c *Customer) {
    o.customer = c
    c.orders = append(c.orders, o)
}
```

**Fixes:** N/A

**Reference:** https://refactoring.guru/change-unidirectional-association-to-bidirectional
