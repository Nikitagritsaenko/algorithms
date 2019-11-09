# Strings
- [Longest Substring Without Repeating Characters](#longest-substring-without-repeating-characters)
- [Generate Parentheses](#generate-parentheses)

## Longest Substring Without Repeating Characters
https://leetcode.com/problems/longest-substring-without-repeating-characters/
Найти наибольшую подстроку, у которой все элементы разные

### Решение "в лоб"

Элементы текущей подстроки хранятся в `map`. Когда находим подстроку, возвращаемся к её началу и сдвигаемся на 1 
символ вправо, очищаем словарь, снова начинаем поиск. Решение не эффективно по времени.

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int N = s.length();
        int length = 0, lastPos = 0;
        Map<Character, Boolean> map = new HashMap<Character, Boolean>();
        int i = 0;
        while (i < N) {
            if (map.containsKey(s.charAt(i))) {
                length = Math.max(length, map.size());
                map.clear();
                i = lastPos;
                lastPos = i + 1;
            }
            map.putIfAbsent(s.charAt(i), true);
            i++;
        }
        return Math.max(length, map.size());
    }
}
```

### Решение со "скользящим окном"
Здесь берется окно от `i` to `j`. Все элементы, лежащие внутри окна, записаны в `Set`. Если встречается символ, не лежащий в множестве, тогда
окно расширяется вправо, элемент добавляется в множество, обновляется размер окна. Если встретили символ, который уже есть, то окно обрезается слева, 
а элемент множества, который был с левого края,  удаляется.

```java
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        Set<Character> set = new HashSet<>();
        int ans = 0, i = 0, j = 0;
        while (i < n && j < n) {
            // try to extend the range [i, j]
            if (!set.contains(s.charAt(j))){
                set.add(s.charAt(j++));
                ans = Math.max(ans, j - i);
            }
            else {
                set.remove(s.charAt(i++));
            }
        }
        return ans;
    }
}
```
### Решение со "скользящим окном" - оптимизация
Нет смысла каждый раз увеличивать левую границу окна на 1. Пусть рассматриваем символ `s[j]`. 
Пусть в окне `[i, j)` уже есть дубликат `s[j]: s[j] = s[j']`. Тогда можно начать с `i = j' + 1`

```java
public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length(), ans = 0;
        Map<Character, Integer> map = new HashMap<>(); // current index of character
        // try to extend the range [i, j]
        for (int j = 0, i = 0; j < n; j++) {
            if (map.containsKey(s.charAt(j))) {
                i = Math.max(map.get(s.charAt(j)), i);
            }
            ans = Math.max(ans, j - i + 1);
            map.put(s.charAt(j), j + 1);
        }
        return ans;
    }
}
```
## Generate Parentheses
https://leetcode.com/problems/generate-parentheses/solution/

Дано число `n` - количество пар скобок. Вывести все валидные строки длиной `2n`, содержащие эти строки.

Пример: `())(()` - так нельзя

### Решение
Можно перебрать все варианты таких строк (их `2^2n`), из них вывести только валидные

Можно поступить более умным способом - запустить рекурсию с возвратом. Заметим, что все строки в любом случае будут начинаться с символа 
`(`. 

Выход из рекурсии - когда длина строки достигла нужного размера (`2n`)

Если количество открывающих скобок не достигло своего предела (`n`), то ставим открывающую скобку, уходим в рекурсию

Если закрывающих скобок меньше, чем открывающих, надо добавить закрывающую и уйти в рекурсию

Для случая `n = 3` решение напечатается вот так:
```
(
((
(((
((()
((())
((()))
возврат
(()
(()(
(()()
(()())
возврат
(())
(())(
(())()
возврат
()
()(
()((
()(()
()(())
...
```

```java
class Solution {
    public void addBracket(List<String> list, String str, int numOpen, int numClose, int limit) {
        if (str.length() == 2 * limit) { 
            list.add(str);
            return;
        }
        if (numOpen < limit)
            addBracket(list, str + "(", numOpen + 1, numClose, limit);
        if (numClose < numOpen)
            addBracket(list, str + ")", numOpen, numClose + 1, limit);
    }
            
    public List<String> generateParenthesis(int n) {
        List<String> list = new ArrayList<String>();
        addBracket(list, "(", 1, 0, n);
        return list;
    }
}
```

