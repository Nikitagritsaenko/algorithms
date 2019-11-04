# Reverse linked list
https://leetcode.com/problems/reverse-linked-list/

## Решение, которое пришло в голову
Проверим случаи пустого списка и списка из одного элемента. В этих случаях ничего делать не надо.

Единственная сложность задачи - при проходе списка сделать так, чтобы переход к следующему элементу был корректен 
(можно допустить ошибку - не сохранить значение поля `next`, перезаписать его на предыдущий элемент списка, 
а потом вернуться обратно к предыдущему элементу и зациклиться) 

Поэтому используется 2 "указателя" - `prev` и `curr`. 
В цикле с помощью временной переменной `next` происходит разворот и переход к следующему элементу списка. 
Выход - когда `curr == null`. Возвращается `prev`, то есть последний валидный элемент - голова нового списка.


```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null)
            return null;
        if (head.next == null)
            return head;
        
        ListNode prev = head;
        ListNode curr = head.next;
        head.next = null;
        
        while (true) {
            if (curr == null)
                break;
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```
##  Решение - чуть более простое

Тут используем 3 указателя - `prev`, `curr`, `next`. Так значительно проще сделать обмен в цикле. В конце надо развернуть последний элемент списка, поскольку в цикле этого не происходит.

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null)
            return null;
        if (head.next == null)
            return head;
        
        ListNode prev = null, curr = head, next = head.next;
    
        while (next != null) {
            curr.next = prev;
            prev = curr;
            curr = next;
            next = next.next;
        } 
        
        curr.next = prev;
        return curr;
    }
}
```
# Middle of the Linked List
https://leetcode.com/problems/middle-of-the-linked-list

## Что пришло в голову

Пройтись по списку один раз, чтобы узнать его длину.

Посчитать медиану.

Пройтись по списку во второй раз, чтобы извлечь из него медиану.
```java
class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null)
            return null;
        if (head.next == null)
            return head;
        
        int num_elems = 1;
        ListNode curr = head.next;
        
        while (curr != null) {
            num_elems = num_elems + 1;
            curr = curr.next;
        }
        
        int median = 0;
        if (num_elems % 2 == 0)
            median = num_elems / 2 + 1;
        else
            median = Math.round(num_elems / 2.0f);
         
        curr = head;
        int idx = 1;
        while (true) {
            if (idx == median)
                return curr;
            curr = curr.next;
            idx = idx + 1;
        }
        
    }
}
```

## Другие решения
1) Перезаписать список в массив. Потом извлечь из массива медиану. Тут надо заранее знать длину списка (в задаче сказано, что максимальная длина 100, так что надо выделить массив на 100 эл-тов).

2) Очень крутой подход - медленный и быстрый указатель. Медленный прыгает последовательно по элементам, быстрый - через один. Когда быстрый достиг конца списка, вернуть надо медленный.

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}
```

# Linked List Cycle
https://leetcode.com/problems/linked-list-cycle - определить, если ли цикл в связном списке
https://leetcode.com/problems/linked-list-cycle-ii - выдать также узел списка, который этот цикл начинает

## Что пришло в голову

Заведем `hashmap`. Будем по мере прохода списка добавлять туда элементы. Если такой элемент уже был, значит мы зациклились.
```java
public class Solution {
   
    public ListNode detectCycle(ListNode head) {
        Map<ListNode, Boolean> hashMap = new HashMap<>();
        ListNode curr = head;
        while (curr != null) {
            if (hashMap.containsKey(curr))
                return curr; // или true
            hashMap.put(curr, true);
            curr = curr.next;
        }
        return null; // или false
    }
}
```

## Другие решения

#### Через HashSet
Лучше использовать `hashSet`. Здесь нет необходимости хранить пары "ключ-значение", нужны лишь ключи.
```java
public boolean hasCycle(ListNode head) {
    Set<ListNode> nodesSeen = new HashSet<>();
    while (head != null) {
        if (nodesSeen.contains(head)) {
            return true;
        } else {
            nodesSeen.add(head);
        }
        head = head.next;
    }
    return false;
}
```

#### Через fast & slow pointers

Подход с двумя указателями: быстрый и медленный. Медленный прыгает последовательно по элементам, быстрый - через один. Когда быстрый достиг значения `null`, можно сделать вывод, что цикла нет. Если указатели оказались в одном месте, то это значит, что есть цикл.

Сам факт наличия цикла проверен. Теперь, если цикл есть, надо найти его начало. Для этого берем еще один медленный указатель и двигаем его от `head` до тех пор, пока он не пересечется с `fast`. Он пересечется именно в голове списка, что не очень очевидно.

```java
Пусть slow и fast встретитись на позиции p. Пусть длина цикла n, а длина списка до цикла k. 

Нужно вернуть k-ый элемент списка.

slow прошел p позиций. fast прошел 2p позиций.

p = 2p - n * c, где c целое число - количество оборотов fast в цикле

p = n * c

Пусть head и fast встретились через d итераций

Тогда

(p - k) + 2 * d - n * c = d (суть в том, что fast проходит столько же оборотов - c штук)

n * c - k + 2 * d = d + n * c

k = d

d - это позиция head. Она равна k. Нужно вернуть k-ый элемент списка. Так что return head.
```

``` java
public ListNode detectCycle(ListNode head) {
    if (head == null || head.next == null) {
        return null;   // no circle
    }
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        slow = slow.next;
        if (fast == slow) {  // circle detected (в случае первой задачи вернуть true)
            while (head != fast) {
                fast = fast.next;
                head = head.next;
            }
            return head;
        }
    }
    return null; // no circle (в случае первой задачи вернуть false)
}
```
