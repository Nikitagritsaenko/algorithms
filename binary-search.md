## Search in sorted array
https://leetcode.com/problems/binary-search
### Решение
Классический алгоритм бинарного поиска. Опорный элемент берется как середина отрезка. 
Далее левая/правая граница отрезка смещается от опорного элемента на 1 единицу внутрь отрезка 
(опорный элемент уже проверен, нет смысла снова включать его в отрезок). Всё это до тех пор, пока не нашли целевое значение 
(или пока отрезок не превратился в пустой - тогда нет решения)

```java
    public int search(int[] nums, int target) {
        int a = 0, b = nums.length - 1, m;
        while (a <= b) {
            m = a + (b - a) / 2;
            if (nums[m] == target) {
                return m;
            }
            else if (nums[m] > target) {
                b = m - 1;
            }
            else  {
                a = m + 1;
            }

        }
        
        return -1;
    }
```

## Search in rotated sorted array
https://leetcode.com/problems/search-in-rotated-sorted-array

Дан массив, который получен из отсортированного путем поворота вокруг какой-то точки.

Например, 1 2 3 4 5 6 => 3 4 5 6 1 2

Найти в этом массиве значение `target`.

### Решение
Модифицированный бинарный поиск.

Заметим, что если взять в массиве любую точку, то относительно этой точки либо левая часть массива будет отсортированной, либо правая.

Алгоритм следующий:

0. Разделить массив на 2 равные части
1. Понять, какая часть отсортированная
2. Проверить, лежит ли целевое значение внутри этой части (проверить можно по крайним точкам, ведь часть отсортирована!)
3. Если целевое значение лежит в этой части, то продолжить в ней поиск. Если нет - продолжить поиск в оставшемся массиве.
4. Если интервал поиска стал пустым, а значение не найдено - значит его нет, вернуть -1

```java
class Solution {
     public int search(int[] nums, int target) {
        int start = 0;
        int end = nums.length - 1;
          
        while (start <= end) {
            int mid = start + (end - start) / 2;
            if (nums[mid] == target)
                return mid;
        
            if (nums[start] <= nums[mid]) {
                 if (target < nums[mid] && target >= nums[start]) 
                    end = mid - 1;
                 else
                    start = mid + 1;
            } 
            else {
                if (target > nums[mid] && target <= nums[end])
                    start = mid + 1;
                 else
                    end = mid - 1;
            }
        }
        return -1;
    }
}
```

## Find minimum in rotated sorted array

https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/

### Решение

Заметим, что минимумом в этом массиве является как раз именно та точка, по которой массив повернули. Хотя бывает, что массив и не поворачивали, так что ответом может быть и nums[0]

Найдем точку поворота (`pivot`) бинарным поиском.

```java
    public int findMin(int[] nums) {
        int start = 0;
        int end = nums.length - 1;
        
        int pivot = -1;
        
        while (start <= end) {
            
            pivot = start + (end - start) / 2;
            
            if (nums[0] <= nums[pivot]) { //массив от 0 до pivot отсортирован => не подходит нам
                start = pivot + 1;
            } 
            else {
                // массив от 0 до pivot не отсортирован, значит pivot внутри него.
                if (nums[pivot-1] > nums[pivot]) // может быть так, что pivot уже найден, и дальше нет смысла искать
                    return nums[pivot];
                end = pivot - 1;
            }
        }
      
        // если дошли до этой строчки кода, то значит pivot не был найден
        return nums[0];
    }
```
## Find K closest elements in a sorted array
https://leetcode.com/problems/find-k-closest-elements/
Есть отсортированный массив `arr`, число `k`, число `x`. Надо из этого массива вернуть `k` чисел, ближайших к `x`.
Все числа массива положительные.

### Решение
Пусть массив длины n. Очевидно, что задача сводится к поиску ближайшего к числу `x` элемента по индексам [0..n-k-1], 
поскольку нужно вернуть k подряд идущих элементов. При этом, если таких элементов несколько (они повторяются), 
нужно найти тот, который имеет в массиве наименьший индекс.

Для этого в бинарном поиске будем смотреть отрезок [mid..mid+k]

Если отрезок находится справа от `x`, то тогда в условии будет false, end=mid, то есть
исключаем часть, которая находится справа от середины, идем влево - то есть в сторону `x`.

Если отрезок слева, то идём вправо в сторону `x`. 

Если `x` внутри отрезка, то идём в ту сторону отрезка, к которой `x` находится ближе. 

Таким образом, алгоритм сходится к ближайшему `x` числу. Индекс этого числа минимальный - 
если таких чисел несколько, то будет найдено число с наименьшим индексом, поскольку в случае
равенства в условии выполняется end=mid, то есть идём влево. 

```java
class Solution {
    public List<Integer> findClosestElements(int[] arr, int k, int x) {
        int start = 0, end = arr.length - k;
        while (start < end) {
            int mid = start + (end - start) / 2;
            if (x - arr[mid] > arr[mid+k] - x) {
                start = mid + 1;
            }
            else {
                end = mid;
            }
        }   
        
        List<Integer> res = new ArrayList(k);
        for (int i = 0; i < k; ++i) {
            res.add(arr[start + i]);
        }
        
        return res;
    }
}
```
