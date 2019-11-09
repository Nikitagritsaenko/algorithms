# Arrays

- [Two Sum](#two-sum)
- [Container With Most Water](#container-with-most-water)

## Two Sum
https://leetcode.com/problems/two-sum/solution/

Есть целевое значение `target` и входной массив `nums`. Нужно в массиве найти 2 индекса, которые соответствуют 2 числам массива, сумма которых равна `target`

### Решение

#### Что приходит в голову

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] indices = new int[2];
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target)
                    return new int[] {i, j};
            }
        }
            
        return indices;
    }
}
```

#### Как сделать быстрее?

Лучший способ маппинга индекса и элемента - хэш-таблица. Поиск происходит за `O(1)`, в случае коллизий дегенерирует до `O(n)`

Сначала бежим по массиву, чтобы установить соответствие, а потом - чтобы искать нужный индекс. 

Итак, это будет работать за  `O(n)`, а не за квадрат

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        map.put(nums[i], i);
    }
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement) && map.get(complement) != i) {
            return new int[] { i, map.get(complement) };
        }
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

#### Еще чуть лучше

Будем делать 1 проход, а не 2. Нужного индекса в мапе может и не оказаться, но потом он добавится всё равно. И можно будет уже для этого индекса искать пару (которая уже есть)
 
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] { map.get(complement), i };
        }
        map.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two sum solution");
}
```



## Container With Most Water
https://leetcode.com/problems/container-with-most-water/

Дан входной массив - высоты вертикальных столбиков. Представим, что между каждой парой столбиков можно наполнить простанство водой.
Нужно найти максимальнуй объем воды, который можно налить между двумя столбиками.

Можно перебрать все пары столбиков, посчитать объем воды между каждой парой, найти максимум. Но это нерационально.

Алгоритм может работать за `O(n)`, а не за квадрат. 

### Решение

Метод двух указателей 

Первый изначально указывает на самый левый столбик, второй - на самый правый. Будем двигать их друг к другу так, чтобы суммарный объем воды 
имел шанс увеличиться. Для этого сдвигаем тот столбик, чья высота меньше. С каждым шагом алгоритма указатели приближаются, что гарантирует выход
и работу за `O(n)`


```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int res = 0;
        while (left < right) {
            res = Math.max(res, Math.min(height[left], height[right]) * (right - left));
            if (height[left] < height[right])
                left++;
            else 
                right--;
        }
        return res;
    }
}
```
