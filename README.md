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

`dequeue` in our earlier implementation of the `Queue` data structure has a runtime of `O(n)` as each removal requires us to shift all the remaining elements in memory, see Swift documentation for [`removeFirst()`](https://developer.apple.com/documentation/swift/array/2884646-removefirst) runtime complexity. Since Swift does not handle this optimization for us we need to implement a more optimal approach that will decrease the runtime to `O(1)`.

![optimized sketch](https://user-images.githubusercontent.com/1819208/96497279-723c4680-1218-11eb-81a6-d1c61b548c87.jpg)

```swift
struct Queue<T> {
  private var elements = [T?]()
  private var head = 0
  
  public var isEmpty: Bool {
    return count == 0 
  }
  
  public var count: Int {
    // since we are moving head we need to account for its current position
    // e.g
    // [nil, nil, "Bob", "Allison", "Ed"]
    // [0,    1,   head,  2,          3]
    // array count is 5
    // head is at position 2
    // count = array count - head position
    // 5 - 2 = 3
    return elements.count - head
  }
  
  public var front: T? {
    guard !isEmpty else {
      return nil
    }
    return elements[head] // we get to the front by indexing with head's position
  }
  
  public mutating func enqueue(_ element: T) {
    elements.append(element)
  }
  
  public mutating func dequeue() -> T? {
    guard !isEmpty, let element = elements[head] else {
      return nil
    }
    elements[head] = nil // remove element from queue
    head += 1 // move head forward by 1

    // on occasion we will need to clean up unused positons in the array (house-cleaning)
    // here we will get a percentage of the head / array count
    let percentage = Double(head)/Double(elements.count)
    if elements.count > 20 && percentage > 0.25 {
      elements.removeFirst(head) // remove all elements up to and including the head index
      head = 0 // reset head
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
