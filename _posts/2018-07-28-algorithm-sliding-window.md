---
layout: post
title: 알고리즘 - 슬라이딩 윈도우 알고리즘
description: Algorithm - String Sliding Window Algorithm
modified: 2018-07-28
tags: [algorithm]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Leetcode에서 Find All Anagrams in a String 문제(Find All Anagrams in a String)를 풀다가 막혀서 discuss를 봤는데 Sliding Window Algorithm으로 풀이한 것이 있었다. 나중에 다시 볼 때 이해를 용이하게 하기 위해 간단히 그 방법을 남겨둔다. 

## 접근법

알고리즘의 기본적인 접근법은 다음과 같다. 

String에서 anagram의 조건은 다음과 같다. 
1. anagram으로 주어진 character들의 사용 빈도와 일치해야 한다.  anagram이 'aab'라면 2개의 'a'와 하나의 'b'를 찾아야 한다. 'aba', 'aab', 'baa'가 가능할 것이다. 
2. 비교한 String의 길이가 pattern으로 주어진 anagram의 길이와 같아야 한다. 예를 들어 anagram이 'aab'라면 'aacb'와 비교했을때 빈도가 'a'와 'b'의 빈도가 일치하지만 중간에 c가 껴있어 그 길이가 다르다. 

이 알고리즘에서는 anagram에 존재하지 않는 character가 나타났는지를 확인하는 방식을 사용하지 않는다. 

1번 조건은 HashMap과 counter를 이용하여 조건을 달성했는지 확인한다. Map에 character와 frequency를 넣고, 이것이 0에 도달하면 counter(초기값은 Map의 size(), 즉, anagram에 사용된 character 갯수)를 하나 줄여서 counter가 0에 도달하면 그 조건에 달성한 것으로 본다. 이때 주의할 점은 frequency는 negative값이 될 수 있다는 것이다. 예를 들어 anagram이 'aab'이고 입력 string이 'aaaaa'라면 'a' entry의 값의 초기값은 2 이지만 마지막에 도달했을때는 -3이 되어 있을 것이다. 물론 counter는 0이 되어 있어 anagram에 사용된 횟수만큼의 'a'가 출현했음을 표시한다. 
2번 조건은 start와 end의 길이를 anagram길이와 비교함으로써 달성한다. 예를 들어 비교하고자하는 string이 'aaab'이고 anagram이 'aab'이면 첫번째 a에서는 길이가 anagram의 길이보다 하나 더 길기 때문에 조건에 달성안되지만, start가 두번째 'a'인 경우에는 길이가 일치하게 된다. 

## 알고리즘

위의 접근법을 이용한 알고리즘은 다음과 같다. 

1. 입력 anagram을 이용해서 Map과 counter를 초기화 한다. 
2. start와 end index를 0으로 초기화 한다. 
3. start가 먼저 출발해서 index를 하나씩 증가시키면서 1번 조건이 달성되었는지 확인한다. 달성이 안되면 전체 입력 string에 걸쳐 필요한 character들이 모두 출현하지 않은 것이다. 
4. 만약 counter가 0이 되면, (즉, 필요한 character들을 모두 찾으면) end를 증가시키면서 두 번째 조건인 길리조건이 만족하는지 확인한다. start-end가 anagram과 길이가 같으면 anagram을 찾은 것이 된다. 
그리고 counter가 0인 경우 다시 anagram에서 사용된 character를 다시 찾아서, 찾는 경우 이번에는 반대로 Map의 entry의 값을 1 증가 시킨다. 다음 예를 보자. 입력 anagram이 'aab'인데 counter가 0이 된 시점에 'cccaacaacab'라고 생각해보자. 
'c'를 만나면 그냥 계속 end를 앞으로 진행시키다가 'a'를 만나면 위에서 말한바와 같이 길이를 비교한다. 3보다 길기 때문에 일치하지 않는 것으로 판단한다. 그리고 map의 'a' 값을 1 증가시킨다. 하지만 처음 두개의 'a'가 계속 나와도 아직 그 값이 0보다 커지진 않았다. 왜냐하면 처음 start를 진행시킬때 이것이 negative값이 되어 있기 때문이다. 마지막에서 두번째 'a'를 만다면 'a'의 값이 '1'보다 커져서 counter를 1증가 시킨다. 이때가 바로 'acab'인 시점이다. 이때 길이 조건을 확인했는데 anagram길이보다 길기 때문에 아닌 것으로 판단하고, 'a'의 값을 1 증가시키면, 값이 0보가 커지게 되어 counter의 값도 0이 아닌 1이 된다. 
5. counter의 값이 0이 아니게 되면 end의 증가를 멈추가 start가 증가하게 된다. 위의 경우는 'cab'의 'c'가 end가 멈춘부분이고 'b'가 start가 멈추어 있던 부분이다. 이제 'cab'뒤에 'a'가 하나 더 있어 'cabaa'라고 생각해보자. counter는 1이고 'a'의 값은 1, 'b'의 값은 0이 시점이다. start를 하나 더 증가한 뒤 'b'바로 뒤의 'a'를 map에서 확인해서 값을 하나 줄여 0이 되었기에 counter가 다시 0이 된다. 
6. 그러면 end가 다시 시작하는데 'c'는 무시하고 지나가고 'a'를 만나서 길이를 비교한다. start - end가 3이기에 anagram을 찾은 것이 된다. 다시 'a'의 값을 1 증가시켜서 counter가 1이 되고, start가 다시 증가해서 이번에는 역시 'a'를 만나 값을 다시 줄이고 counter도 줄여서 0이 되어 다시 end가 움직여 anagram을 또 찾았음을 확인하게 된다. 

## code

```java
public class Solution {
    public List<Integer> findAnagrams(String s, String t) {
        List<Integer> result = new LinkedList<>();
        if(t.length()> s.length()) return result;
        Map<Character, Integer> map = new HashMap<>();
        for(char c : t.toCharArray()){
            map.put(c, map.getOrDefault(c, 0) + 1);
        }
        int counter = map.size();
        
        int begin = 0, end = 0;
        int head = 0;
        int len = Integer.MAX_VALUE;
        
        
        while(end < s.length()){
            char c = s.charAt(end);
            if( map.containsKey(c) ){
                map.put(c, map.get(c)-1);
                if(map.get(c) == 0) counter--;
            }
            end++;
            
            while(counter == 0){
                char tempc = s.charAt(begin);
                if(map.containsKey(tempc)){
                    map.put(tempc, map.get(tempc) + 1);
                    if(map.get(tempc) > 0){
                        counter++;
                    }
                }
                if(end-begin == t.length()){
                    result.add(begin);
                }
                begin++;
            }
            
        }
        return result;
    }
}
```
