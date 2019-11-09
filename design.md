# Design
- [Implement Queue using Stacks](#implement-queue-using-stacks)

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
