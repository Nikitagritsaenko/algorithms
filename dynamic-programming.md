# Dynamic programming

- [Backpack](#backpack)
- TODO [Climbing Stairs](#climbing-stairs)
- TODO [Coin Change](#coin-change)
- TODO [Coin Change 2](#coin-change-2)
- TODO [Longest Increasing Subsequence](#longest-increasing-subsequence)
- TODO [Longest Common Subsequence](#longest-common-subsequence)
- TODO [Word Break](#word-break)
- TODO [Unique Paths](#unique-paths)
- TODO [Unique Paths II](#unique-paths-ii)
- TODO [Jump Game](#jump-game)
- TODO [Jump Game II](#jump-game-ii)
- TODO [House Robber](#house-robber)
- TODO [House Robber II](#house-robber-ii)
- TODO [Decode Ways](#decode-ways)
- TODO [N-Queens](#n-queens)
- TODO [N-Queens-II](#n-queens-ii)

## Backpack
https://stepik.org/lesson/13259/step/5?unit=3444

Первая строка входа содержит целые числа W, n - вместимость рюкзака и число золотых слитков. Следующая строка содержит n целых чисел - веса слитков (они отсортированы по возрастанию!). Найдите максимальный вес золота, который можно унести в рюкзаке.

Sample Input:

10 3
1 4 8

Sample Output:

9

### Решение

Используется таблица D[0..W, 0..n]. В её ячейках - оптимальные значения для веса золота в рюкзаке. D[i,j] == d означает, что в рюкзаке вместимостью i с j предметами оптимальный вес d.

Таблица заполняется снизу вверх - от более простой задачи к более сложной. 

Ключевая формула:
```java 
D[j][i] = Math.max(D[j][i-1], D[j - w[i]][i - 1] + с[i]);  
// с[i] - стоимость слитка. В данном случае рассматриваем вес в качестве критерия, так что c[i] == w[i]
```

Если предмет не положить, то `D[j][i] = D[j][i-1]` (то есть то же самое, что и оптимальное заполнение для i-1 предмета)

Если предмет положить, то `D[j][i] = D[j - w[i]][i - 1] + с[i]`. 

`D[j - w[i]][i - 1]` - это оптимальное заполнение рюкзака без этого предмета.

Из двух возможных действий выбираем наилучшее путём выбора максимального веса.

Возвращаем ответ `D[W][n]` - оптимальное заполнение для рюкзака весом W из n предметов, что и требовалось в задаче.

```java
import java.util.Scanner;

class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int W = sc.nextInt(); // вместимость рюкзака
        int n = sc.nextInt(); // число золотых слитков
        int[] w = new int[n+1]; // веса слитков
        
        w[0] = 0;
        for (int i = 1; i <= n; i++) {
            w[i] = sc.nextInt();
        }
        
        int [][] D = new int[W+1][n+1];
        
        for (int i = 0; i <= W; i++) {
            D[i][0] = 0; // если нельзя использовать ни один предмет, то и класть ничего нельзя
        }
        for (int i = 0; i <= n; i++) {
            D[0][i] = 0; // если вместимость рюкзака 0, то и положить в него ничего нельзя
        }


        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= W; ++j) {
                D[j][i] = D[j][i-1]; // заполним рюкзак i-1 предметом, а потом попытаемся положить i-ый предмет
                if (w[i] <= j) { // если предмет помещается в рюкзак, то рассматриваем его
                    D[j][i] = Math.max(D[j][i], D[j - w[i]][i - 1] + w[i]); 
                    // в более общем алгоритме нужно прибавить не w[i], а c[i] - стоимость предмета 
                }
            }
        }
        
        System.out.println(D[W][n]);
    }
}
```

## Climbing stairs
https://leetcode.com/problems/climbing-stairs/

### Решение

```java
```

## Coin change
https://leetcode.com/problems/coin-change/

### Решение

```java
```

## Coin change 2
https://leetcode.com/problems/coin-change-2/

### Решение

```java
```

## Longest Increasing Subsequence
https://leetcode.com/problems/longest-increasing-subsequence/

### Решение

```java
```

## Longest Common Subsequence
https://leetcode.com/problems/longest-common-subsequence/

### Решение

```java
```

## Word Break
https://leetcode.com/problems/word-break/

### Решение

```java
```

## Unique Paths
https://leetcode.com/problems/unique-paths/

### Решение

```java
```

## Unique Paths II
https://leetcode.com/problems/unique-paths-ii/

### Решение

```java
```

## Jump Game
https://leetcode.com/problems/jump-game/

### Решение

```java
```

## Jump Game II
https://leetcode.com/problems/jump-game-ii/

### Решение

```java
```

## House Robber
https://leetcode.com/problems/house-robber/

### Решение

```java
```

## House Robber II
https://leetcode.com/problems/house-robber-ii/

### Решение

```java
```

## Decode Ways
https://leetcode.com/problems/decode-ways/

### Решение

```java
```

## N-Queens
https://leetcode.com/problems/n-queens/

### Решение

```java
```

## N-Queens II
https://leetcode.com/problems/n-queens-ii/

### Решение

```java
```
