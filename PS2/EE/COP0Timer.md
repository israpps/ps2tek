---
sort: 9
---

# EE COP0 Timer

## `$09` - COP0.Count
- 0-31  Timer count value
Count increments automatically every EE cycle.

## `$11` - COP0.Compare
- 0-31  Timer compare value
  
When Count==Compare, a COP0 timer interrupt is signaled. Writing to Compare acknowledges and clears the pending interrupt.
