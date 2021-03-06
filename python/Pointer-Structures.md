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

면접 단골 질문이라고 함. linked list에서 infinite loop 찾는방법

```
def checkInfinite(self):
    p1 = p2 = self
    while p1 != None and p2 != None:
        if p2.next == None: return False
            
        p1 = p1.next
        p2 = p2.next.next
        if p1 == p2: return True
    return False
```

## Prefix Tree

Prefix Tree: data structure that compactly stores string

![image](https://i.imgur.com/eXpIIq4.png)

- Dictionary를 사용하여 pointer를 구현
- 각 box를 하나의 dictionary로

Python dictionary: `d[k] = v`
- value로 nested dictionary를 저장
- 각각의 dict는 최대 26개의 다른 dict objects를 refer

```
wordkey = '\n'  # can be any character not 'a..z'


class PrefixTree:
    '''
    Data structure that compactly stores strings
    # example: init, in
    {
        'i': {
            'n': {
                'i': {
                    't': {
                        'isEnd': True
                    }
                },
                'isEnd': True  #?
            }
        }
    }
    '''
    def __init__(self, *args):
        self.head = {}

        if args:
            for arg in args:
                self.add(arg)

    def add(self, value: str) -> bool:
        '''Add avlue to prefix tree. Return TRUE if updated'''
        d = self.head

        while len(value) > 0:
            c = value[0]
            if c not in d:
                d[c] = {}
            d = d[c]
            value = value[1:]
        if wordkey in d:
            return False
        d[wordkey] = True
        return True

    def __contains__(self, value: str):
        '''
        determine if value in prefix tree

        >>> d = PrefixTree()
        >>> d.add('in')
        >>> d.add('inch')
        >>> 'in' in d
        True
        >>> 'inc' in d
        False
        >>> 'inch' in d
        True
        ''' 
        d = self.head
        while len(value) > 0:
            c = value[0]
            if c not in d:
                return False
            d = d[c]
            value = value[1:]
        return wordkey in d

    def __repr__(self):
        return f'{self.head}'
```

### Summary
- Provide structural basis for recursive data structures
- 코드가 복잡하지만 특정 상황에서 나은 시간복잡도로 구현 가능
