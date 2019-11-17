# Design
- [Implement Queue using Stacks](#implement-queue-using-stacks)
- [Implement Stack using Queues](#implement-stack-using-queues)
- [Min Stack](#min-stack)
- [LRU Cache](#lru-cache)

## Implement Queue using Stacks
https://leetcode.com/problems/implement-queue-using-stacks/

### Решение - Two Stacks
Будем представлять очередь в виде двух стеков. Также будем хранить переменную front - голову первого стека.

```java
   private Stack<Integer> s1, s2;
   private int front;
```

**push**

Будем класть элементы **всегда в первый стек**. Если первый стек пуст, то сохраним его нижний элемент в переменную front.

```java
/*  Push element x to the back of queue. */
    public void push(int x) {
        if (s1.empty())
            front = x;
        s1.push(x);
    }
```

**pop**

Во второй стек будем перекладывать все элементы с первого, затем забирать с него верхний элемент (он был последним в первом стеке,
то есть пришел первым в очередь)

Все остальные элементы на первый стек можно не возвращать. Теперь два стека таковы - первый стек пустой, а второй содержит все элементы
очереди, причем голова лежит сверху. Так что если второй стек не пуст, то из очереди легко сделать pop. Если хотим сделать push, то делаем
это в первый стек, сохраняем значение front. При непустом втором стеке этот элемент не является головой очереди, поэтому, чтобы его снять,
теперь надо снять все элементы второго стека. Так и должно быть - ведь у нас очередь.

```java
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (!s2.isEmpty())
            return s2.pop();
        
        while (!s1.isEmpty()) {
            s2.push(s1.pop());
        }
        
        return s2.pop();
    }
```
**peek**

Голова очереди - это либо значение front (если второй стек пуст), либо верхушка второго стека.

```java
    /** Get the front element. */
    public int peek() {
        if (!s2.isEmpty()) {
            return s2.peek();
        }
        return front;
    }
```
**empty**

В очереди нет элементов тогда, когда оба стека пусты.

``` java
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return (s1.isEmpty() && s2.isEmpty());
    }
```
## Implement Stack using Queues
https://leetcode.com/problems/implement-stack-using-queues

### Two Queues

Будем стек представлять как 2 очереди. Также будем в переменной `top` хранить элемент, добавленный в "стек" последним.

```java
class MyStack {
    
    private Queue<Integer> q1 = new LinkedList<>();
    private Queue<Integer> q2 = new LinkedList<>();
    private int top;
```

**push**

Всегда будем добавлять элемент в первую очередь. Так так этот элемент должен быть на вершине стека, сохраним его в top.

```java
    /** Push element x onto stack. */
    public void push(int x) {
        q1.add(x);
        top = x;
    }
```

**pop**

Извлечь элемент из стека - значит снять с верхушки стека. Или же взять последний элемент первой очереди. Для этого все элементы очереди перекладываются на вторую очередь. Причем в переменную top запишется предпоследний элемент первой очереди - это новая верхушка стека. Далее очереди меняем местами. Вторая очередь - это та же первая очередь, но без хвоста. Порядок элементов не нарушен. Таким образом, первая очередь остается без последнего элемента, а вторая остается пустой.

```java
    /** Removes the element on top of the stack and returns that element. */
    public int pop() {
        while (q1.size() > 1) {
            top = q1.remove();
            q2.add(top);
        }
        int res = q1.remove();
        Queue<Integer> tmp = q1;
        q1 = q2;
        q2 = tmp;
        
        return res;
    }
```
**top**

Значение top сохраняется при добавлении в первую очередь. И операция pop меняет значение переменной top на предпоследний элемент первой очереди. Таким образом, в переменной top всегда лежит значение верхушки стека, вне зависимости от количества применённых до этого операций pop и push.

```java
   public int top() {
        return top;
    }
```

**empty**

Так как стек хранится по сути только в первой очереди, то стек пуст тогда, когда пуста первая очередь.

```java
    /** Returns whether the stack is empty. */
    public boolean empty() {
        return (q1.isEmpty());
    }
```
**Получился на самом деле стек на одной очереди, вторая очередь вспомогательная, её можно создавать локально внутри операции pop.**

## Min Stack
https://leetcode.com/problems/min-stack/

Реализовать стек, в котором можно за O(1) выполнить операции: push, pop, top, getMin. Сложность именно в getMin.

### Решение
Можно реализовать 2 стека. В первый стек всегда делать push, во второй стек добавлять элемент только в том случае, если он меньше или равен верхушке стека. Если элемент удаляем из первого стека, то надо его удалить и из второго. Для каждого состояния первого стека (после нескольких операций push, pop) всегда на верхушке второго стека будет лежать минимум.

```java
    Deque<Integer> stack = new ArrayDeque<Integer>();
    Deque<Integer> min_stack = new ArrayDeque<Integer>();
    
    public void push(int x) {
        if (min_stack.isEmpty()) {
            min_stack.push(x);
        }
        else if (x <= min_stack.peek()) {
            min_stack.push(x);
        }
        stack.push(x);
    }
    
    public int pop() {
        int elem = stack.pop();
        if (elem == min_stack.peek()) {
            min_stack.pop();
        }
        return elem;
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return min_stack.peek();
    }
```
## LRU Cache
https://leetcode.com/problems/lru-cache/

Реализовать структуру данных Least Recently Used (LRU) cache с операциями `get(key)` и `put(key, value)` за O(1)

### Решение - Double linked list + Hashmap

Будем использовать двусвязный список с фиктивными head и tail. Так удобнее добавлять элементы в голову списка и удалять элементы из
хвоста списка. HashMap нужна для быстрого доступа к любому элементу двусвязного списка.

В голове списка будут лежать более новые элементы, в хвосте - более старые. Добавляется элемент всегда в голову (после фиктивной головы). Удалиться элемент может по причине того, что к нему долго не обращались (он находится в хвосте списка) и при этом приходит новый элемент, который и вытеснит его.

Реализуем операции: 
- `addNode(node)` - добавить элемент после фиктивной головы
- `removeNode(node)` - удалить элемент из двусвязного списка
- `moveToHead(node)` - удалить элемент и поместить в голову списка
- `popTail()` - удалить хвост списка (элемент перед фиктивным хвостом)

```java
public class LRUCache {

    class ListNode {
        int key;
        int value;
        ListNode prev;
        ListNode next;
    }
    
    private Map<Integer, ListNode> cache = new HashMap<>();
    private ListNode head, tail;
    private int capacity;
```

```java
    /* add new node after head */
    private void addNode(ListNode node) {
        node.prev = head;
        node.next = head.next;

        head.next.prev = node;
        head.next = node;
    }
    
    /* remove an existing node from the linked list. */
    private void removeNode(ListNode node) {
        ListNode prev = node.prev;
        ListNode next = node.next;
        prev.next = next;
        next.prev = prev;
    }

    /* Move certain node in between to the head. */
    private void moveToHead(ListNode node){
        removeNode(node);
        addNode(node);
    }

    /* pop the current tail. */
    private ListNode popTail(){
        ListNode res = tail.prev;
        this.removeNode(res);
        return res;
    }

    public LRUCache(int capacity) {
        this.capacity = capacity;

        head = new ListNode();
        head.prev = null;

        tail = new ListNode();
        tail.next = null;

        head.next = tail;
        tail.prev = head;
    }
 ```

**get(key)**

Ищем элемент в мапе по ключу, если его нет, то вернем -1.

Если элемент найден, то возвращается его value, но при этом сам элемент помещается в голову списка, поскольку к нему только что обратились.

```java
    public int get(int key) {
        ListNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        
        moveToHead(node);
        return node.value;
    }
```

**put(key, value)**

Если уже есть элемент с таким ключом, то обновим его value, а затем поместим элемент в голову, поскольку он новый.

Если элемент новый, то добавляем его в cache, создаем узел списка, добавляем его в голову списка. Теперь проверяем, не был ли превышен размер кеша: если да, то удалим элемент из конца списка, а затем удалим его из кеша по ключу.

```java
    public void put(int key, int value) {
        ListNode node = cache.get(key);

        if (node == null) {
            ListNode newNode = new ListNode();
            newNode.key = key;
            newNode.value = value;

            cache.put(key, newNode);
            addNode(newNode);

            if (cache.size() > capacity) {
                ListNode tail = popTail();
                cache.remove(tail.key);
            }
        } 
        else {
            node.value = value;
            moveToHead(node);
        }
    }

}
```
