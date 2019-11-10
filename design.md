# Design
- [Implement Queue using Stacks](#implement-queue-using-stacks)
- [Implement Stack using Queues](#implement-stack-using-queues)
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
