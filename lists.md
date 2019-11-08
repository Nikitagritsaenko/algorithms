## Reverse linked list
https://leetcode.com/problems/reverse-linked-list/

### Решение, которое пришло в голову
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
###  Решение - чуть более простое

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
## Middle of the Linked List
https://leetcode.com/problems/middle-of-the-linked-list

### Что пришло в голову

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

### Другие решения
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

## Linked List Cycle
https://leetcode.com/problems/linked-list-cycle - определить, если ли цикл в связном списке
https://leetcode.com/problems/linked-list-cycle-ii - выдать также узел списка, который этот цикл начинает

### Что пришло в голову

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

### Другие решения

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
        if (fast == slow) {  // circle detected 
            // (!) (в случае первой задачи вернуть true)
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

## Intersection of Two Linked Lists
https://leetcode.com/problems/intersection-of-two-linked-lists/

Найти узел, в котором 2 связных списка начинают пересекаться. Списки не содержат циклов. Списки обязательно пересекаются.

Можно пройтись по первому списку, запомнить все элементы в `HashSet`, потом идти по второму списку - смотреть, были ли такие значения в первом списке. Если были, то получим ответ. (O(m+n) time, O(m) memory)

Предыдущий подход не оптимален по памяти

### Решение: O(m+n) time, O(1) memory

2 указателя `pA` и `pB`. Оба начинаются с головы своих списков. Как только `pA` доходит до конца списка, он становится `null`. В этом случае перенаправим его в голову списка `B`. То же с указателем `pB` - перенаправим его в голову списка `A`. 

Будем с цикле смещать эти указатели на 1 позицию. Как только указатели встретились, вернем ответ.

Почему этот ответ верный?
```
Пусть длина списков n и m.

Пусть длина необщих частей соответственно k и p. Тогда длина общей части: n - k = m - p

Для определенности пусть первый список длиннее второго. 
Тогда начало первого списка будем считать точкой отчета (нулем).

Начало второго списка имеет координату k - p = n - m

Пусть сделано m шагов: первый указатель в позиции m, второй - в позиции n

Сделаем еще k шагов: первый указатель в позиции m + k > n => он в позиции m + k - n второго списка, 
то есть в позиции p относительно начала второго списка, то есть в позиции k относительно начала первого списка.
Второй указатель будет в позиции k первого списка.

Указатели встречаются в позиции k, элемент по которой нам и нужно вернуть, ведь это начало пересечения списков
```

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null)
            return null;
        ListNode pA = headA, pB = headB;
        
        while (pA != pB) {    
            if (pA == null) {
                pA = headB;
            }
            else {
                pA = pA.next;
            }
            
            if (pB == null) {
                pB = headA;
            }
            else {
                pB = pB.next;
            }
        }
        
        return pA;
    }
}
```
## Palindrome Linked List

https://leetcode.com/problems/merge-two-sorted-lists/

Является односвязный список палиндромом, или нет?

### Решение, которое пришло в голову (O(n) время, O(1) память)

Будем использовать решения предыдущих задач

1. Найдем середину в списке
2. Развернем правую часть списка (правая часть - начиная с середины)
3. Левую часть списка сравним с правой, по результатам выдаём ответ. 

Вначале напишем условия для проверки случаев n == 0, n == 1.

Для сравнения левой и правой части будем использовать 2 указателя (`pLeft` `pRight`). Если вдруг значения по указателям не совпали, то можно сразу выдать ответ `false`. При сравнении списков остановимся тогда, когда указатели встретились (`java pLeft == pRight`) - это происходит в списках нечетной длины. Если списки четной длины, то сработает условие (`pLeft.next == pRight`). Эти условия означают, что сравнение списков закончено, причём ответ `false` еще не был дан, так что списки равны, то есть исходный список - палиндром.

```java
public boolean isPalindrome(ListNode head) {
        if (head == null)
            return true;
        if (head.next == null)
            return true;
        
        ListNode med = middleNode(head);
        ListNode rightPart = reverseList(med);
      
        ListNode pLeft = head, pRight = rightPart;
   
        while (true) { 
            if (pLeft.val != pRight.val) {
                return false;
            }
            if (pLeft == pRight || pLeft.next == pRight) {
                break;
            }
            pLeft = pLeft.next;
            pRight = pRight.next;
        }
        
        return true;
        
    }
```

## Merge Two Sorted Lists

### Решение
Если один из списков пуст, то вернем другой.

Далее будем добавлять элементы в список так же, как в merge sort.  Из двух входных списков выбираем минимальный элемент. Список, из которого был выбран элемент, сдвигается. Как только один из списков закончился, нужно к хвосту результата прикрепить второй список.

Изначально список инициализуется фиктивным элементом, так что возвращается второй элемент этого списка.

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null)
            return l2;
        if (l2 == null)
            return l1;
        
        ListNode merged = new ListNode(0); // dummy
        ListNode head = merged;
        
        while (l1 != null && l2 != null) {
            
            if (l1.val <= l2.val) {
                merged.next = l1;
                l1 = l1.next;
            }
            else {
                merged.next = l2;
                l2 = l2.next;
            }
            
            merged = merged.next;
        }
        
        if (l1 == null) {
            merged.next = l2; 
        }
        
        if (l2 == null) {
            merged.next = l1;
        }
        
        return head.next;      
    }
}
```
## Reorder List

Переформировать входной односвязный список следующим образом: первый - последний - второй - предпоследний - третий...

### Решение
1. Найдем средний элемент списка
2. Правую часть развернем
3. Из левой и правой части составим новый список (см. цикл)

```java
class Solution {
    /* define middleNode, reverseList */
    
    public void reorderList(ListNode head) {
        ListNode med = middleNode(head);
        ListNode rightPart = reverseList(med);
      
        ListNode pLeft = head, pRight = rightPart;
   
        while (pLeft != pRight && pLeft.next != pRight) { 
            ListNode nextLeft = pLeft.next, nextRight = pRight.next;
            
            pLeft.next = pRight;
            pRight.next = nextLeft;
           
            pLeft = nextLeft;
            pRight = nextRight;
        }
    }
}
```
