# Strings
- [Template for substring tasks](#template-for-substring-tasks)
- [Longest Substring Without Repeating Characters](#longest-substring-without-repeating-characters)
- [Longest Repeating Character Replacement](#longest-repeating-character-replacement)
- [Generate Parentheses](#generate-parentheses)
- [Minimum Window Substring](#minimum-window-substring)
- [Group Anagrams](#group-anagrams)
- [Valid Parentheses](#valid-parentheses)
- [](#)
- [](#)
- [](#)

## Template for substring tasks

```c++
int findSubstring(string s){
        vector<int> map(128,0);
        int counter; // для проверки корректности подстроки
        int begin = 0, end = 0; // окно
        int d; // размер подстроки

        for() { /* инициализировать нужным образом мапу */ }

        // цикл по всей строке
        while(end < s.size()){
            
            if(map[s[end]] ??) { // проверить критерий качества, обновить counter }
            
            map[s[end]]-- (++);
            // увеличить / уменьшить количество метрики для символа конца окна
            
            // цикл, в котором выполняется критерий поиска ответа (критерий сделан с помощью counter), 
            // при выходе из цикла получим оптимальный ответ (максимально сдвинутый слева)
            
            while (/*критерий поиска*/) { 
                // обновить d до получения оптимального ответа
                // сузить окно слева
                begin++;
                
                // если выполняется условие, ведущее к нарушению критерия (т.е если окно больше не подходит), то
                if (map[s[begin]] ??){ 
                   // изменить counter (т.е подать сигнал о том, что окно не подходит, дальше сужать некуда, из цикла надо выйти)
                }
                
                map[s[begin]]++ (--); // увеличить / уменьшить количество метрики для символа начала окна
            }  

            // пересчитать ответ (d) с помощью begin и end
            
            // расширить окно справа
            end++;
        }
        
        // вернуть ответ или пустую строку, если не нашли окно
        return d;
  }
```

## Longest Substring Without Repeating Characters
https://leetcode.com/problems/longest-substring-without-repeating-characters/
Найти наибольшую подстроку, у которой все элементы разные

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

## Longest Repeating Character Replacement
https://leetcode.com/problems/longest-repeating-character-replacement

Дана строка, состоящая из заглавных букв, можно заменить k букв на другие буквы. После таких k замен найти максимально возможное количество одних и тех же подряд идущих букв.

### Решение
Используем sliding window. Будем хранить массив, в котором будет лежать количество каждого символа в окне. Размер окна не уменьшается внутри цикла. Окно либо остается той же длины (когда его обрезают слева и расширяют справа), либо увеличивается (когда только расширяют справа). Ответом является максимальная длина такого окна.

#### Код с объяснением
```java
class Solution {
    public int characterReplacement(String s, int k) {
        int len = s.length();
        int[] count = new int[26];
        int start = 0, maxCount = 0, maxWindowSize = 0; // окно [start, end]
        for (int end = 0; end < len; end++) {
            count[s.charAt(end) - 'A']++; // на правой границе окна встретили символ => увеличим его count на 1
            maxCount = Math.max(maxCount, count[s.charAt(end) - 'A']); // количество самых часто встречающихся символов в окне
            int currWindowSize = end - start + 1; // текущий размер окна
            // если currWindowSize == maxCount, то окно заполнено одним и тем же символом
            // eсли currWindowSize > maxCount, то окно заполнено не только самым часто встречающимся символом
            // если currWindowSize - maxCount == k, то внутри окна можно сделать k замен 
            // ecли currWindowSize - maxCount > k, то больше не можем делать замены символов
            if (currWindowSize - maxCount > k) {
                // окно обрезаем слева, уменьшаем количество вхождений левого символа
                count[s.charAt(start) - 'A']--;
                start++;
                currWindowSize--;
            }
            maxWindowSize = Math.max(maxWindowSize, currWindowSize);
        }
        return maxWindowSize;
    }
}
```
#### Более компактная запись

```java
class Solution {
    public int characterReplacement(String s, int k) {
        int len = s.length();
        int[] count = new int[26];
        int start = 0, maxCount = 0, maxLength = 0;
        for (int end = 0; end < len; end++) {
            maxCount = Math.max(maxCount, ++count[s.charAt(end) - 'A']);
            if (end - start + 1 - maxCount > k) {
                count[s.charAt(start) - 'A']--;
                start++;
            }
            maxLength = Math.max(maxLength, end - start + 1);
        }
        return maxLength;
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

## Minimum Window Substring

https://leetcode.com/problems/minimum-window-substring

Даны 2 строки: S и T. Найти в строке S окно минимальной длины, которое содержит все символы строки T (при том с нужной кратностью!).

### Решение

Метод sliding window. Используем 2 мапы: `dictT` хранит количество уникальных символов в строке T; `windowCounts` хранит
для текущего окна количество вхождений в него каждого символа.

Окно расширяем справа в цикле. Когда окно заполнилось, сужаем его слева во вложенном цикле до тех пор, пока левая граница не станет оптимальной (т.е заполненное окно будет минимальной длины)

```java
class Solution {
    public String minWindow(String s, String t) {
        // 0. Проверка на пустоту
        if (s.length() == 0 || t.length() == 0) {
            return "";
        }

        // 1. Построить map с количеством всех уникальных символов в строке T
        Map<Character, Integer> dictT = new HashMap<Character, Integer>();
        
        for (int i = 0; i < t.length(); i++) {
            int count = dictT.getOrDefault(t.charAt(i), 0);
            dictT.put(t.charAt(i), count + 1);
        }
        
        // 2. Инициализация окна 
        //количество символов, необходимое внутри окна (по сути минимально возможный р-р окна)
        int required = dictT.size(); 
    
        int l = 0, r = 0; // окно
        int formed = 0; // количество символов, которые вошли в окно с нужной кратностью
        
        // 3. Построить map с количеством вхождений в окно всех уникальных символов строки T 
        Map<Character, Integer> windowCounts = new HashMap<Character, Integer>();
        
        int[] ans = {-1, 0, 0}; // (window length, left, right)

        while (r < s.length()) {
            // 1. Добавить правый элемент окна в map окна (число вхождений увеличиваем на 1)
            char c = s.charAt(r);
            int count = windowCounts.getOrDefault(c, 0);
            windowCounts.put(c, count + 1);

            // 2. Проверить, набралось ли достаточное количество данного символа в окне
            if (dictT.containsKey(c) && windowCounts.get(c).intValue() == dictT.get(c).intValue()) {
                formed++;
            }

            // 3. Уменьшаем окно слева до того момента, когда оно перестаёт подходить под критерий formed == required
            while (l <= r && formed == required) {
                c = s.charAt(l);
                
                // сохраняем минимальное окно
                if (ans[0] == -1 || r - l + 1 < ans[0]) {
                    ans[0] = r - l + 1;
                    ans[1] = l;
                    ans[2] = r;
                }

                // удалим левый символ из окна
                windowCounts.put(c, windowCounts.get(c) - 1); // уменьшим на 1 количество его вхождений
                
                // если количество вхождений этого символа в окно стало меньше, чем нужно, то критерий больше не выполняется
                if (dictT.containsKey(c) && windowCounts.get(c).intValue() < dictT.get(c).intValue()) {
                    formed--;
                }

                l++;
            }

            // 4. Увеличить окно справа
            r++;   
        }

        return ans[0] == -1 ? "" : s.substring(ans[1], ans[2] + 1);
    }
}
```

## Group Anagrams
https://leetcode.com/problems/group-anagrams

Дан список строк. Нужно вернуть список из списков, где эти строки сгруппированы на анаграммы.

### Решение 1 - формирование уникального кода для каждой анаграммы

Для каждой строки посчитаем частоты вхождения каждого символа. Частоты представлены в виде массива на 26 элементов. Очевидно, что
строки являются анаграммами друг друга в том случае, если соответствующие 26-разрядные массивы совпадают. Поэтому на основе массива
генерируется код, по которому хранится список анаграмм. Если у двух строк коды разные, то они будут лежать в разных списках.

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        
        for (String str : strs) {
            int[] freq = new int[26];
            
            for (int i = 0; i < str.length(); i++) {
                freq[str.charAt(i) - 'a']++;
            }
            
            StringBuilder sb = new StringBuilder("");
            for (int i = 0; i < 26; i++) {
                sb.append(' ').append(freq[i]);
            }
            String key = sb.toString();
            
            if (!map.containsKey(key)) {
                map.put(key, new ArrayList<>());
            }

            map.get(key).add(str);
        }
        return new ArrayList(map.values());
    }
}
```

### Решение 2 - сортировать строки
Строки являются анаграммами друг друга в том случае, если они совпадают после сортировки. Поэтому в качестве ключа может служить отсортированная строка.

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        HashMap<String, ArrayList<String>> map = new HashMap<>();
        
        for (String string : strs){
            
            char[] stringChar = string.toCharArray();
            Arrays.sort(stringChar);
            String sortString = String.valueOf(stringChar);
            
            map.putIfAbsent(sortString, new ArrayList<String>());
            map.get(sortString).add(string);
        }
        return new ArrayList<>(map.values());
    }
}
```

## Valid Parentheses
https://leetcode.com/problems/valid-parentheses

Дана строка, содержащая скобки {} [] (). Определить, является ли комбинация скобок валидной.

### Решение с помощью стека

Будем класть скобку на стек, если она является открывающей (то есть не является ключом в словаре). Если скобка закрывающая, то
посмотрим на вершину стека - была ли там открывающая скобка того же типа? Если нет, то ответ `false`. Если нашли валидную пару скобок, то элемент можно снять со стека и проверять дальше.

Таким образом, стек в итоге окажется пуст, если строка со скобками валидная.

```java
class Solution {
    public boolean isValid(String s) {
        Deque <Character> stack = new ArrayDeque<>();
        Map <Character, Character> map = new HashMap<>();
        map.put('}', '{');
        map.put(']', '[');
        map.put(')', '(');
        
        for (int i = 0; i < s.length(); i++) {
            Character c = s.charAt(i);
            if (!map.containsKey(c)) {
                stack.push(c);
            }
            else {
                char top = stack.isEmpty() ? ' ' : stack.pop();
                if (top != map.get(c)) {
                    return false;
                }
            }

        }
        
        return stack.isEmpty();
    }
}
```
