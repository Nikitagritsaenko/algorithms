# Dynamic programming

- [Backpack](#backpack)
- [Climbing Stairs](#climbing-stairs)
- [Coin Change](#coin-change)
- [Coin Change 2](#coin-change-2)
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

Вы поднимаетесь по лестнице. Требуется n шагов, чтобы добраться до вершины. Каждый раз вы можете подняться на 1 или 2 шага. Сколькими способами вы можете подняться на вершину?

Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step

### Решение 1 - рекурсия с запоминанием

Можно сделать обычной рекурсией, но тогда сложность по времени будет O(2^n). В этом подходе - O(n).

В основе рекурсивная формула: `climbStairs(i, n) = climbStairs(i+1, n) + climbStairs(i+2, n)`. Если i будет равно n, то значит есть такой путь, значит прибавим 1 к ответу. Если n превышен, то такого пути нет, конец рекурсии. Если n не достигнут, дальше уходим в рекурсию.

```java
public class Solution {
    public int climbStairs(int n) {
        int memo[] = new int[n + 1];
        return climb_Stairs(0, n, memo);
    }
    public int climb_Stairs(int i, int n, int memo[]) {
        if (i > n) {
            return 0;
        }
        if (i == n) {
            return 1;
        }
        if (memo[i] > 0) {
            return memo[i];
        }
        memo[i] = climb_Stairs(i + 1, n, memo) + climb_Stairs(i + 2, n, memo);
        return memo[i];
    }
}
```

### Решение 2 - динамическое программирование

```java
public class Solution {
    public int climbStairs(int n) {
        if (n == 1) {
            return 1;
        }
        
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
}
```

### Решение 3 - числа Фибоначчи

Можно заметить, что ответ к задаче - число Фибоначчи f(n)

```java
public class Solution {
    public int climbStairs(int n) {
        if (n == 1) {
            return 1;
        }
        
        int a = 1;
        int b = 2;
        
        for (int i = 3; i <= n; i++) {
            b = a + b;
            a = b - a;
        }
        return b;
    }
}
```

Можно также по формуле, но это решение менее надежное, хоть и более быстрое (степень вычисляется за логарифм). Ненадежность в том, что при большом n теряется точность.

```java
public class Solution {
    public int climbStairs(int n) {
        double sqrt5=Math.sqrt(5);
        double fibn=Math.pow((1+sqrt5)/2,n+1)-Math.pow((1-sqrt5)/2,n+1);
        return (int)(fibn/sqrt5);
    }
}
```

## Coin change
https://leetcode.com/problems/coin-change/

Вам выдаются монеты разных номиналов и общая сумма денег amount. Какое минимальное число монет может составить эту сумму? Если эта сумма денег не может быть составлена какой-либо комбинацией монет, верните -1.

### Решение

Заполняем массив по каждой возможной суммарной стоимости (от 0 до amount). 

Ключевая формула: `dp[i] = Math.min(dp[i], dp[i-coin] + 1)`. Смотрим в массиве, можно ли дополнить сумму текущей монетой. Если да, то берем оптимальное число монет для суммы с вычетом текущей монеты + 1. При этом берется минимум по всем вычитаемым монетам.

Возвращаем `dp[amount]`, т.к. это требуется в задаче. Массив инициализировался значениями `amount + 1`, комбинации из такого числа монет невозможны. Поэтому если `dp[amount] > amount`, то комбинация не найдена.

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        
        for (int i = 1; i <= amount; i++) {
            for (int coin : coins) {
                 if (i >= coin) {
                    dp[i] = Math.min(dp[i], dp[i-coin] + 1);
                 }
            }           
        }
        
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

## Coin change 2
https://leetcode.com/problems/coin-change-2/

Предыдущая задача, но нужно посчитать количество всевозможных комбинаций монет, которые составят сумму amount.

### Решение

Казалось бы, можно поменять основную формулу предыдущей задачи и получить что-то такое:

```java
for (int i = 1; i <= amount; i++) {
    for (int coin : coins) {
        if (i >= coin) {
            dp[i] += dp[i-coin];
        }
    }           
}
```

Однако такое решение неверно, потому что в `dp[i]` добавляются повторяющиеся комбинации. Причина в том, что тут все монеты перебираются много раз. Нужно сделать как в подходе с рюкзаком: пусть можно использовать только 1 монету, только 1 и 2 монеты, только 1, 2, 3 и т.д. То есть надо поменять порядок в цикле. Тогда будет составлена таблица из всех возможных сумм и всех возможных количеств разрешенных монет.

Основная формула была бы такая: `dp[i][j] = dp[i-1][j] + (j >= coins[i-1] ? dp[i][j-coins[i-1]] : 0)`, гду i - число разрешенных монет, j - сумма.

`dp[i][j]` зависит только от `dp[i-1][j]` (количество комбинаций для этой же суммы, но когда в наборе на 1 монету меньше) и `dp[i][j-coins[i]]` (количество комбинаций для того же числа монет, но для суммы меньшей на цену новой монеты).

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[][] dp = new int[coins.length+1][amount+1];
        dp[0][0] = 1;
        
        for (int i = 1; i <= coins.length; i++) {
            dp[i][0] = 1;
            for (int j = 1; j <= amount; j++) {
                dp[i][j] = dp[i-1][j] + (j >= coins[i-1] ? dp[i][j-coins[i-1]] : 0);
            }
        }
        return dp[coins.length][amount];
    }
}
```

Таблица может быть вырождена в одномерный массив. В формуле `dp[j] += dp[j-coin]` по сути `dp[i-1][j]` учитывается как `+=` (потому что в `dp[j-coin]` уже лежит результат для комбинаций из тех же монет, но кроме новой), а `dp[j-coin]` это и есть `dp[i][j-coins[i-1]]`.

```java
class Solution {
    public int change(int amount, int[] coins) {
        int[] dp = new int[amount + 1];
        dp[0] = 1;
        
        for (int coin : coins) {
            for (int j = coin; j <= amount; j++) {
                dp[j] += dp[j-coin];
            }
        }
        
        return dp[amount];
    }
}
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
