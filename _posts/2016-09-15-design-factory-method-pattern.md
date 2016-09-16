---
layout: post
title: Design - Factory method pattern
description: Design - Factory method pattern
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


Factory method pattern은 동일한 API를 이용해 다양한 product를 가변적으로 생성하기 위해 사용한다. 
Argument에 어떤 종류의 객체를 생산하고 싶은지를 넘겨주고 factory 내부에서 이를 확인하여 해당하는 객체를 만들어서 넘겨준다. 
익숙한 pattern인데 Java에서 어떻게 구현할 수 있는지 살펴보자. 
 


### 1. 요구사항 

Vehicle을 만드는데, 종류는 Car, Truck, ElectricCar가 있다. 최대한 dependency를 줄이면서 구현하라. 


### 2. 구현 방안

보통은 factory의 구현자체에서 비교를 통해 instance 종류를 바꾸게 되는데, 이때 비교는 enum을 이용하곤 한다. 
이 경우 새로운 Vehicle이 추가될 경우 enum도 추가하고 factory에서 비교 및 생성하는 부분도 수정해야 한다. 
확장시 modification되는 포인트를 factory 자체에 두지 말고 종류를 지정하는 enum에 위치하도록 하여 modification을 최소화하자. 

#### 2.1 Vehicle interface와 class들 

```java
//Vehicle.java
public interface Vehicle {
	void startEngine();
}

//Car.java
public class Car implements Vehicle {

	@Override
	public void startEngine() {
		System.out.println("started a small engine of car...");
	}

}

//Truck.java
public class Truck implements Vehicle {

	@Override
	public void startEngine() {
		System.out.println("started a big engine of truck...");
	}

}


//ElectricCar.java
public class ElectricCar implements Vehicle {

	@Override
	public void startEngine() {
		System.out.println("started a electirc engine of car...");
	}

}
```

#### 2.2 App

우리는 아래처럼 VehicleFactory를 사용하기를 바란다. 

```java
//App.java
public class App {
	public static void main(String argc[]) {
		VehicleFactory vehicleFactory = new VehicleFactory();
		Vehicle vehicle = vehicleFactory.getVehicle(VehicleType.TRUCK);
		vehicle.startEngine();
	}
}
```

이 경우 출력은 다음과 같아야 한다. 

```bash
started a big engine of truck...
```


#### 2.3 VehicleFactory와 VehicleType


VehicleType을 주목하라!

1. enum 내부에서 각 값마다 method를 각기 다르게 구현
2. 외부로의 interface는 abstract method로 정의

```java
//VehicleType.java
public enum VehicleType {
	TRUCK {
		public Vehicle getVehicle(){
			return new Truck();
		}
	}, 
	CAR {
		public Vehicle getVehicle(){
			return new Car();
		}
	}, ELECTRICCAR {
		public Vehicle getVehicle(){
			return new ElectricCar();
		}
	};
	
	abstract Vehicle getVehicle();
}

//VehicleFactory.java
public class VehicleFactory {
	public Vehicle getVehicle(VehicleType vehicleType) {
		return vehicleType.getVehicle();
	}
}

```

### 3. 분석

확장에는 열려있고 수정에는 닫혀있어야 한다는 Open Close Principle(OCP) 관점에서 분석을 해보자. 

#### 3.1 일반적인 factory method 구현 시 

아래의 일반적으로 짤 법한 코드로는 Factory 자체를 수정해야 한다. 

```java
//VehicleType.java
public enum VehicleType {
	TRUCK, CAR, ELECTRICCAR, BUS //<-- 수정!!
}


//VehicleFactory.java
public class VehicleFactory {
	public Vehicle getVehicle(VehicleType vehicleType) {
		if(vehicleType.equals(VehicleType.TRUCK)) {
			return new Truck();
		} else if(vehicleType.equals(VehicleType.CAR)) {
			return new Car();
		} else if(vehicleType.equals(VehicleType.ELECTRICCAR)) {
			return new ElectricCar();
		} else if(vehicleType.equals(VehicleType.BUS)) { //<- 수정!!
 			return new ElectricCar();
		} 
		
		return null;
	}
}
```

#### 3.2 위의 factory method 구현 시  

위의 2.3의 코드를 보면 만약 새로운 Type의 Vehicle인 Bus가 추가 되도라도 VehicleType에 추가만 해주면 된다. 수정 포인트가 하나가 된다. 

```java
//VehicleType.java
public enum VehicleType {
	TRUCK {
		public Vehicle getVehicle(){
			return new Truck();
		}
	}, 
	CAR {
		public Vehicle getVehicle(){
			return new Car();
		}
	}, ELECTRICCAR {
		public Vehicle getVehicle(){
			return new ElectricCar();
		}
	}, BUS {                       // <-- 수정!!
		public Vehicle getVehicle(){
			return new Bus();
		}
	};
	
	abstract Vehicle getVehicle();
}

//VehicleFactory.java
public class VehicleFactory {
	public Vehicle getVehicle(VehicleType vehicleType) {
		return vehicleType.getVehicle();
	}
}
```
