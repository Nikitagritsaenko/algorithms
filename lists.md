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
