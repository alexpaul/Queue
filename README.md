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

## More optimized `dequeue()`

`dequeue` in our earlier implementation of the `Queue` data structure has a runtime of `O(n)` as each removal requires us to shift all the remaining elements in memory. Since Swift does not handle this optimization for us we need to implement a more optimal approach the will decrease the runtime to `O(1)`.

```swift
struct Queue<T> {
  private var elements = [T?]()
  private var head = 0
  
  public var isEmpty: Bool {
    return elements.isEmpty
  }
  
  public var count: Int {
    return elements.count - head
  }
  
  public var front: T? {
    guard !isEmpty else {
      return nil
    }
    return elements[head]
  }
  
  public mutating func enqueue(_ element: T) {
    elements.append(element)
  }
  
  public mutating func dequeue() -> T? {
    guard !isEmpty, let element = elements[head] else {
      return nil
    }
    elements[head] = nil
    head += 1
    
    let percentage = Double(head)/Double(elements.count)
    if elements.count > 20 && percentage > 0.25 {
      elements.removeFirst(head)
      head = 0
    }
    
    return element
  }
}

var queue = Queue<String>()
queue.enqueue("Josh")
queue.enqueue("Tim")
queue.enqueue("Bob")
queue.enqueue("Allison")
queue.enqueue("Ed")

queue.front // Josh

queue.dequeue() // Josh

queue.front // Tim

queue.dequeue() // Tim

queue.front // Bob

queue.count // 3
```

## Resources 

1. [Ray Wenderlich - Queue](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Queue)
