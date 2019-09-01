---
layout: post
title: Design - Builder pattern
description: Design - Builder pattern
modified: 2016-09-15
tags: [java, design]
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


Builder pattern를 언제 사용하고 어떻게 만들 수 있는지 살펴보자. 

### 1. 언제 사용할까?

Builder Pattern은 Constructor의 attribute 수가 많은 경우 사용된다. attribute가 어느 정도 갯수 이상이 되면, constructor의 숫자도 많아지고 순서를 지켜야 하기 때문에 사용이 꽤나 귀찮아 진다. 
이때 Builder pattern을 이용하면 구현은 조금 복잡하지만 유연한 constructor을 만들수 있다. 

### 2. 클라이언트에서의 사용 방법

다음과 같이 클라이언트에서 Builder pattern의 constructor를 호출할 수 있다. 

```java

public class App {
	public static void main(String argc[]) {
		User bob = new User.Builder("bob", "bob@exmple.com")
				.firstName("Bob")
				.lastName("Max")
				.phoneNumber(822795555)
				.build();
		System.out.println(bob);
	}
}

```

### 3. Builder pattern 

다음이 builder pattern 구현의 예이다. 아래 내용을 명심하자. 

1. 원래 목표로한 User class의 constructor는 private으로 하고, argument가 Builder instance이다. Builder instance를 받아 그 attribute로 자신의 attribute값을 세팅한다. 
2. Builder class는 원래 만들고자 하는 class의 inner class이며, public static 이다. 
3. Builder class의 attribute는 원래 만들고자 한 class의 attribute와 동일하다. 
4. Required와 optional attribute로 구분하고 Builder class의 constructor는 Required attribute들을 argument로 한다. 
5. optional attribute등을 채우는 setter가 Builder 내에 있다. 
6. build() method에서 User instance를 만들어 반환한다. 


위와 같이 함으로써 User bob = User.Builder().....build()를 지원할 수 있게 된다. 

```java

public class User {

	private String userName; //Required
	private String email; //Required

	private String firstName; //Optional
	private String lastName; //Optional
	private int phoneNumber; //Optional
	private String address; //Optional


	private User(Builder builder) {
		this.userName = builder.userName;
		this.email = builder.email;
		this.firstName = builder.firstName;
		this.lastName = builder.lastName;
		this.phoneNumber = builder.phoneNumber;
		this.address = builder.address;
	}


	@Override
	public String toString() {
		return "User [userName=" + userName + ", email=" + email + ", firstName=" + firstName + ", lastName=" + lastName
				+ ", phoneNumber=" + phoneNumber + ", address=" + address + "]";
	}

	public static class Builder {

		private String userName; //Required
		private String email; //Required

		private String firstName; //Optional
		private String lastName; //Optional
		private int phoneNumber; //Optional
		private String address; //Optional

		public Builder(String userName, String email) {
			this.userName = userName;
			this.email = email;
		}
		
		public Builder firstName(String value) {
			this.firstName = value;
			return this;
		}

		public Builder lastName(String value) {
			this.lastName = value;
			return this;
		}
		
		public Builder phoneNumber(int value) {
			this.phoneNumber = value;
			return this;
		}
		
		public Builder address(String value) {
			this.address = value;
			return this;
		}
		
		public User build() {
			return new User(this);
		}
	}
}

```
