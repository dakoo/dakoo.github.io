---
layout: post
title: Java - Comparator Interface 사용
modified: 2016-09-12
tags: [java]
---

예제를 통해 Comparator interface의 사용 방법을 살펴보자. 
Comparator를 사용하기 위해서는 int compare(Object o1, Object o2)를 Override해야 한다.

### 1. 요구사항 

score를 큰 것부터 작은 순으로 정렬하고, 같을 경우엔 name을 알파벳 순으로 정렬하는 것이다. 


입력은 다음과 같다. 

```bash
5
amy 100
david 100
heraldo 50
aakansha 75
aleksa 150
```

출력은 다음과 같다. 

```bash
aleksa 150
amy 100
david 100
aakansha 75
heraldo 50
```

### 2. 구현


```java
//Checker.java
import java.util.Comparator;

class Checker implements Comparator<Player> {

	@Override
	public int compare(Player o1, Player o2) {
		if( o2.score == o1.score){
			o1.name.compareTo(o2.name);
		}
		return o2.score - o1.score;
	}
}

//Player 
class Player{
    String name;
    int score;
    
    Player(String name, int score){
        this.name = name;
        this.score = score;
    }
}

//Solution.java
import java.util.Arrays;
import java.util.Scanner;

class Solution {

    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int n = scan.nextInt();

        Player player[] = new Player[n];
        Checker checker = new Checker();

        for(int i=0; i < n; i++){
            player[i] = new Player(scan.next(), scan.nextInt());
        }
        scan.close();

        Arrays.sort(player, checker);
        for(int i = 0; i < player.length; i++){
            System.out.printf("%s %s\n", player[i].name, player[i].score);
        }
    }
}
```
