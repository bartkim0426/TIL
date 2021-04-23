# Pointer Structures

## Linked List

### Pointer-Based data structure
- Contiguous memory (array)는 약점이 있음: remove, insert -> O(N)
  - 한개의 value를 제거하면 다른 것들도 옮겨야됨
  - inserting도 마찬가지
- Linked List가 이런 문제를 해결하기 위해 보통 사용됨 (C, Pascal 등의 언어에서)

### LinkedNode object
- value와
- 인접한 LinkedNode object에 대한 레퍼런스를 가짐
  - 인접한 object가 없다면 None
- Object reference는 보통 pointer라고 불린다

```
class LinkedNode:
    def __init__(self, value, tail=None):
        self.value = value
        selfnext = tail
```

### Poniter-Based Data Structure: Operations

Efficient operation: Both O(1)
- Remove first value from LinkedList
- Prepend new value to LinkedList

```
class LinkedNode:
    def __init__(self, value, tail=None):
        self.value = value
        self.next = tail

class LinkedList:
    def __init__(self, *start):
        self.head = None

        for _ in start:
            self.prepend(_)

    def prepend(self, value):
        '''Add value to front of the list. O(1)'''
        self.head = LinkedNode(value, self.head)

    def remove(self, value):
        '''remove specific value from list'''
        n = self.head
        last = None
        while n != None:
            if n.value == value:
                if last is None:
                    self.head = self.head.next
                else:
                    last.next = n.next
                return True
            last = n
            n = n.next
        return False

    def pop(self):
        '''Remove first value from list. O(1)'''
        if self.head is None:
            raise Exception('Empty list')
        val = self.head.value
        self.head = self.head.next
        return val

    def __iter__(self):
        n = self.head
        while n != None:
            yield n.value
            n = n.next

    def __repr__(self):
        return 'LinkedList:[' + ','.join(map(str, self)) + ']'
```

## Queues

위에서 만든 LinkedList를 활용하여 Queue를 구현 (First In, First Out)

LinkedList와 다른점은
- Queue는 head노드 뿐만 아니라 tail 노드도 가지고 있음

실제로는 이를 구현할 필요 없이 `collections.deque`를 사용하면 됨
- 직접 만든 QueueLinkedList보다 훨씬 성능이 좋음 (5x)

```
class LinkedNode:
    def __init__(self, value, tail=None):
        self.value = value
        self.next = tail

class QueueLinkedList:
    def __init__(self, *start):
        self.head = None
        self.tail = None

        for _ in start:
            self.append(_)

    def append(self, value):
        '''Add value to end of queue'''
        newNode = LinkedNode(value, None)
        if self.head is None:
            self.head = self.tail = newNode
        else:
            self.tail.next = newNode
            self.tail = newNode

    def pop(self):
        '''Remove first value from queue'''
        if self.head is None:
            raise Exception('Queue is empty')
        val = self.head.value
        self.head = self.head.next
        if self.head is None:
            self.tail = None
        return val

    def isEmpty(self):
        '''Determine if queue is empty'''
        return self.head == None

    def __iter__(self):
        '''Iterator of values in queue'''
        n = self.head
        while n != None:
            yield n.value
            n = n.next

    def __repr__(self):
        return 'QueueLinkedList:[' + ','.join(map(str, self)) + ']'
```

## Project: Detect cycles in Linked List


