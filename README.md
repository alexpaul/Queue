# Queue

Queue implementation in Swift. 

## Queue implementation using an array 

```swift
struct Queue<T> {
  private var elements = [T]()
  
  public var isEmpty: Bool {
    return elements.isEmpty
  }
  
  public var count: Int {
    return elements.count
  }
  
  public var front: T? {
    return elements.first
  }
  
  public mutating func enqueue(_ element: T) {
    elements.append(element)
  }
  
  public mutating func dequeue() -> T? {
    guard !isEmpty else {
      return nil
    }
    return elements.removeFirst()
  }
}

// 4 <- 2 <- 1
var queue = Queue<Int>()
queue.enqueue(4)
queue.enqueue(2)
queue.enqueue(1)

queue.front // 4

queue.dequeue() // 4

queue.front // 2
```
