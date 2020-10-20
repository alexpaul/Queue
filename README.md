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

![optimized sketch](https://user-images.githubusercontent.com/1819208/96574980-2c29c600-129e-11eb-8555-01324ba5ec42.jpg)

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

## Queue implemenation using a Linked List

#### `Node`

```swift
class Node<T: Equatable>: Equatable {
  var value: T
  var next: Node<T>?
  weak var previous: Node<T>? // doubly list, use of weak to prevent retain cycle
  init(_ value: T) {
    self.value = value
  }
  static func ==(lhs: Node, rhs: Node) -> Bool {
    return lhs.value == rhs.value && lhs.next == rhs.next
  }
}
```

#### `LinkedList`

```swift
struct LinkedList<T: Equatable> {
  // data structure
  private var head: Node<T>?
  private var tail: Node<T>?
  
  // private(set) prevents external changes
  // private(set) public var allows access but not modification outside the LinkedList
  private(set) public var count = 0
  
  public var isEmpty: Bool {
    return head == nil
  }
  
  public var last: T? {
    return tail?.value
  }
  
  public var first: T? {
    return head?.value
  }
  
  public mutating func append(_ element: T) {
    count += 1
    let newNode = Node(element)
    guard let lastNode = tail else {
      head = newNode
      tail = newNode
      return
    }
    lastNode.next = newNode
    newNode.previous = lastNode
    tail = newNode
  }
  
  @discardableResult
  public mutating func removeLast() -> T? {
    // 1
    // empty state
    guard let lastNode = tail else {
      return nil
    }
    count -= 1
    // 2
    // one element in the list
    if head == tail { // Node needs to conform to Equatable
      let removedValue = head?.value
      head = nil
      tail = nil
      return removedValue
    }
    
    // 3
    // more than one element in the list
    let removedValue = lastNode.value
    tail = lastNode.previous
    lastNode.previous = nil
    return removedValue
  }
  
  public mutating func removeFirst() -> T? {
    guard let first = head else {
      return nil
    }
    let removedValue = first.value
    head = head?.next
    count -= 1
    return removedValue
  }
}


var list = LinkedList<Int>()
list.append(1)
list.append(5)
list.append(0)

list.front // 1
list.back // 0

list.removeFirst() // 1

list.first // 5

list.count // 2

list.removeFirst() // 5
list.removeFirst() // 0

list.count // 0

list.removeFirst() // nil

list.first // nil
```

#### Challenge: Implement a `Queue` using the `LinkedList` above

```swift 
// code here
```

<details>
  <summary>Solution</summary>

```swift
struct Queue<T: Equatable> {
  // data structure
  private var elements = LinkedList<T>()
  
  public var isEmpty: Bool {
    return elements.isEmpty
  }
  
  public var count: Int {
    return elements.count
  }
  
  public var first: T? {
    return elements.first
  }
  
  public mutating func enqueue(_ element: T) {
    elements.append(element)
  }
  
  public mutating func dequeue() -> T? {
    return elements.removeFirst()
  }
}
```

</details>


## Resources 

1. [Ray Wenderlich - Queue](https://github.com/raywenderlich/swift-algorithm-club/tree/master/Queue)
