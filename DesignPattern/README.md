# Composite

![image](https://user-images.githubusercontent.com/51703260/140633928-205dd18b-c314-49f8-83e8-baeccf12d8b5.png)

### Composite Pattern 이란?
Composite Pattern은 객체들을 트리 구조로 구성한 다음, 이러한 구조를 개별 객체인 것처럼 사용할 수 있는 구조 설계 디자인 패턴이다.

### Problem
> Composite Pattern을 사용하는 것은 어플리케이션의 핵심 모델을 트리로 나타낼 수 있는 경우에만 의미가 있다.

![image](https://user-images.githubusercontent.com/51703260/140634082-16812f69-8d0a-42bd-8bde-53f71d07d38f.png)

예를 들어, `Product` 및 `Box`라는 두 가지 유형의 객체가 있다고 가정해 보자. 

`Box`에는 여러 `Product`와 여러 개의 작은 `Box`가 포함될 수 있다. 이러한 작은 상자에는 일부 `Product` 또는 더 작은 `Box` 등이 포함될 수 있다.

그 다음 이러한 클래스를 사용하는 주문 시스템을 만들기로 결정했다고 추가적으로 가정해 보자. 

주문에는 박스 포장이 없는 `Prodcut`와, 작은 Box와 Product들로 채워진 `Box`가 포함될 수 있다.

이 때, 주문의 총 가격을 어떻게 결정해야할까?

당장 떠오르는 러프한 결정 방법으로는 포장된 상자를 모두 풀고 모든 제품을 살펴본 다음 합계를 계산하는 방법이 있다. 이 방법은 현실세계에서 이런식으로 할 수 있다.

하지만, 프로그램에서는 이건 그렇게 간단한 문제가 아니다.

그렇다면 어떤 해결 방법이 있을까?

### Solution
Composite pattern을 적용한다면, 토탈 가격을 계산할 수 있는 공통 인터페이스를 선언하고 `Product`와 `Box`가 이를 구현하는 방식으로 문제를 해결할 수 있다.

이러한 구조는 어떻게 작동할까? 

`Product`의 경우, 단순히 Product의 가격을 리턴한다. 

`Box`의 경우 Box에 들어 있는 각 아이템들을 살펴보고 각각 아이템에 대한 가격을 모두 구한 다음 결과적으로 이 상자에 대한 토탈 가격을 리턴한다. 

![image](https://user-images.githubusercontent.com/51703260/140634390-d3312d75-37d4-4e82-b6a9-342709211c47.png)

만약 `Box`의 아이템이 `더 작은 Box`라면, 재귀적으로 접근하여 작은 Box 또한 자신이 담겨있는 큰 Box와 동일한 매커니즘으로 가격을 구할 수 있다.

<br/>

이러한 접근 방식의 가장 큰 장점은 트리를 구성하는 객체들의 구체적인 클래스에 대해 신경을 쓰지않아도 된다는 점이다.

`Box`속 객체가 `Product`인지 또 `다른 Box`인지 알 필요없이 공통 인터페이스를 통해 모두 동일하게 처리할 수 있다. 

### Structure

![image](https://user-images.githubusercontent.com/51703260/140637232-3bc42df8-2d7a-4409-a3d1-2f0e637adc8e.png)

Composite Pattern의 구조는 위에서 설명했듯 트리구조이고, 크게 4가지로 구조를 구분할 수 있다.

1. `Component` : Component 인터페이스는 트리의 단일 객체(like `Product`)와 복합 객체(like `Box`) 모두에게 공통 인터페이스를 제공한다.
2. `Leaf` : Leaf는 일반적인 트리구조에서 Leaf Node와 같은 의미이다. Composite Pattern에서 Leaf는 트리의 단순 요소(like `Product`)만으로 이루어져 있으므로 대부분의 실제 작업을 수행한다. 
3. `Container` : Container는 Composite와 동일한 의미이며, Container는 하위 요소들을 가진 요소(like `작은 Box`를 가진 `Box`)이다. <br/>
    자식들의 구체적인 클래스를 알지못하며 공통 인터페이스를 통해 모든 하위 요소와 함께 작동한다. <br/>
    요청을 받으면 본인이 처리할 수 있는 부분은 직접 처리하고, 하위 요소중 자신과 같은 Container가 있다면 자식 Container에게 작업을 위임하여 결과를 리턴받고 결과를 종합하여 응답한다.
4. `Client` : Client는 Component 인터페이스를 통해 모든 구성요소와 함께 작동한다. 결과적으로 Client는 트리의 단순 요소 또는 복잡한 요소 모두에 대해 동일한 방식으로 작업할 수 있게된다.

### Implementation

지금까지의 `Product` `Box`예시로 Composite Pattern을 구현해보자.

> Product.interface
```java
public interface Products {
	int getPrice();
}
```

> Product.class
```java
public class Product implements Products{
	int price = 1000;
	
	@Override
	public int getPrice() {
		return this.price;
	}
}
```

> Box.class
```java
import java.util.*;

public class Box implements Products{
	List<Products> products = new ArrayList<Products>();
	int price;
	
	public void addProduct(Products product) {
		products.add(product);
	}
	
	@Override
	public int getPrice() {
		for(Products product : products) this.price += product.getPrice();
		return this.price;
	}
}
```

> Main.class
```java
public class Main {
	public static void main(String[] args) {
		Box box1 = new Box();
		box1.addProduct(new Product());
		box1.addProduct(new Product());
		box1.addProduct(new Product());
		
		Box box2 = new Box();
		box2.addProduct(new Product());
		box2.addProduct(new Product());
		box2.addProduct(box1);
		
		Box product = new Box();
		product.addProduct(new Product());
		product.addProduct(box2);
		
		System.out.println(product.getPrice());
	}
}
```

> 출력 : 6000 

### Composite Pattern의 장단점
**장점**
- 객체들이 모두 같은 타입으로 취급되기 때문에 새로운 클래스 추가가 용이하다.
- 단일 객체, 복합 객체 구분하지 않고 코드 작성이 가능하다.

**단점**
- 설계를 일반화 시켜 객체간의 구분, 제약이 힘들다.

정리하자면, 컴포지트 패턴의 장점은 사용자 입장에서는 이게 단일 객체인지 복합 객체인지 신경쓰지 않고 사용할 수 있다는 장점이 있지만 설계가 지나치게 범용성을 갖기 때문에 새로운 요소를 추가할 때 복합 객체에서 구성 요소에 제약을 갖기가 힘들다.

# Bridge

![image](https://user-images.githubusercontent.com/51703260/139539726-1d068a79-2be8-4053-98fe-13c9cdd3e0b2.png)

### Bridge Pattern이란?

큰 클래스 또는 밀접하게 관련된 클래스 집합을 서로 독립적으로 개발할 수 있도록 두 개의 계층(기능 계층과 구현 계층)으로 분리한 디자인 패턴이다.

구현부에서 추상층을 분리하여 각자 독립적으로 변형이 가능하고 확장이 가능하도록 한다. 즉 기능과 구현에 대해서 두 개를 별도의 클래스로 구현을 한다.

![image](https://user-images.githubusercontent.com/51703260/139540332-abdf7c90-daee-46cd-ada2-5e0f9a0b6125.png)

먼저 브릿지 패턴이 왜 필요한지 알아보자. 

### Problem

`Shape`라는 클래스가 `Circle`과 `Square`라는 2개의 서브클래스를 가진다고 가정해보자. 

이 클래스 구조에서 각각 `Red`와 `Blue` 라는 Color를 적용시켜 Shape에 **Color를 통합, 확장시키려고 한다.**

이미 `Shape`에는 `Circle`과 `Square`라는 하위 클래스가 있으므로, 위 이미지와 같이 `BlueCircle` 및 `RedSquare`와 같이 네 개의 클래스 조합을 만들어야 한다.

만약 계속 이런 방식으로 클래스를 확장해 나가서 Shape가 100개, Color가 100개가 된다면 10,000개의 클래스가 필요하게 된다.

이 후 Shape이나, Color를 1개라도 추가시키려면 각 Shape별 혹은 Color별 클래스가 100개씩 추가시키는 작업이 필요하게 되는 것은 큰 문제이다.

이러한 문제를 해결하는 디자인 패턴이 바로 브릿지 패턴이다.

### Solution

위와 같은 문제는 Shape 클래스를 Shape별, Color별 독립적으로 확장하려고 하기 때문에 발생하는 문제인데, 이러한 문제는 계급 상속과 관련된 매우 흔한 문제라고 할 수 있다.

브릿지 패턴은 객체 합성으로 이 문제를 해결한다. 한 클래스 내에 모든 상태나 동작을 포함하는 것이 아닌 아래의 이미지와 같이 원래의 클래스가 새로 확장하려는 상태를 클래스로 분리하여 해당 클래스를 참조하여 조합을 만드는 것이다.

![image](https://user-images.githubusercontent.com/51703260/139540800-86a35324-bb99-48dc-ae37-3db3af9666e7.png)

### 브릿지 패턴 구조

![image](https://user-images.githubusercontent.com/51703260/139540113-b331333f-b29c-46e8-94ea-308bcaad7ae8.png)

- `Client` : 일반적으로 클라이언트는 추상화 작업에만 관심이 있지만 추상화 개체를 구현 개체 중 하나와 연결하는 것은 클라이언트가 해주어야 하는 작업이다.

- `Abstraction` : 기능 계층의 최상위 클래스. 구현 부분에 해당하는 클래스를 인스턴스를 가지고 해당 인스턴스를 통해 구현 부분의 메서드를 호출한다.

- `Refind Abstraction` : 기능 계층에서 새로운 부분을 확장한 클래스

- `Implementation` : Abstraction의 기능을 구현하기 위한 인터페이스 정의

- `ConcreteImplementions` : 실제 기능을 구현한다.

### 브릿지 패턴 구현

![image](https://user-images.githubusercontent.com/51703260/139540304-cb7a48ac-16ba-4c57-9a69-5da09f9050ed.png)

> RemoteControl.class
```java
public class RemoteControl {
    private Device divice;
    
    public RemoteControl(Device divice){
         this.divice = divice;
    }
    
    public void togglePower() {
         if(divice.isEnabled()) divice.disable();
         else divice.enable();
    }
    
    public void volumeDown {
         device.setVolume(device.getVolume() - 10);
    }
    
    public void volumeUp {
         device.setVolume(device.getVolume() + 10);
    }
    
    public void channelDown {
         device.setChannel(device.getChannel() - 1);
    }
    
    public void channelUp {
         device.setChannel(device.getChannel() + 1);
    }
}
```

> AdvancedRemoteControl.class
```java
public class AdvancedRemoteControl extends RemoteControl {
    public void mute() {
         device.setVolume(0);
    }
}
```
 
> Device.interface
```java
public interface Device {
    public boolean isEnabled();
    public void enable();
    public void disable();
    public int getVolume();
    public void setVolume(int percent);
    public int getChannel();
    public void setChannel(int channel);
}
```

> Radio.class
```java
public class Radio implements Device {
    private int volume = 50, channel = 11;
    private boolean isEnabled;

    public boolean isEnabled(){
         return this.isEnabled
    }
    public void enable(){
         this.isEnabled = true;
    }
    public void disable(){
         this.isEnabled = false;
    }
    public int getVolume(){
         return this.volume;
    }
    public void setVolume(int volume){
         this.volume = volume;
    }
    public int getChannel(){
         return this.channel;
    }
    public void setChannel(int channel){
         this.channel = channel;
    }
}
```

> TV.class
```java
public class TV implements Device{
    private int volume, channel;
    private boolean isEnabled;

    public boolean isEnabled(){
         return this.isEnabled
    }
    public void enable(){
         this.isEnabled = true;
    }
    public void disable(){
         this.isEnabled = false;
    }
    public int getVolume(){
         return this.volume;
    }
    public void setVolume(int volume){
         this.volume = volume;
    }
    public int getChannel(){
         return this.channel;
    }
    public void setChannel(int channel){
         this.channel = channel;
    }
}
```

> Main.class
```java
public class Main {
    public static void main(String argsp[])
    {    
        tv = new Tv()
         remote = new RemoteControl(tv);
         remote.togglePower();

         radio = new Radio();
         remote = new AdvancedRemoteControl(radio);
    }
}
```

흔히 `Adapter` 패턴과 `Bridge` 패턴을 헷갈려하는 경우가 많다고 한다. 

`Adapter` 패턴은 **서로 다른 인터페이스(API)를 연결해주는 패턴**이라면, 

`Bridge` 패턴은 **구현 계층과 기능(추상) 계층을 서로 분리, 연결시켜주는 패턴**이다.

### `Bridge` 패턴의 활용
- 여러 플랫폼에서 사용해야 할 그래픽스 및 윈도우 처리 시스템에서 유용하게 쓰인다.
- 인터페이스와 실제 구현부를 서로 다른 방식으로 변경해야 하는 경우에 유용하게 쓰인다.

### `Bridge` 패턴의 장점
- 조합의 개수가 늘어남으로써 발생하는 기하급수적인 클래스 확장을 막을 수 있다.
- 구현을 인터페이스에 완전히 결합시키지 않았기 때문에 구현과 추상화된 부분을 분리시킬 수 있다.
- 추상화된 부분과 실제 구현 부분을 독립적으로 확장할 수 있습니다.
- 추상화된 부분을 구현한 구상 클래스를 바꿔도 클라이언트 쪽에는 영향을 끼치지 않는다.

### `Bridge` 패턴의 장점
- 응집도가 높은 클래스에 적용하면 코드와 디자인이 댜 복잡해진다는 단점이 있다.

# Facade

### Facade Pattern이란?

<p align="center"><img width="70%" alt="99B6F54A5C68D4A91D" src="https://user-images.githubusercontent.com/51703260/136665199-6829f771-bea6-458e-9e8f-b75d4d1098b3.png"></p>

퍼사드란, 프랑스어 Façade 에서 유래된 단어로 **"건물의 겉면"** 을 의미한다.
   
퍼사드 패턴의 목적은 복잡한 **서브시스템(내부 구조)** 을 거대한 **클래스(외벽)** 로 감싸서 편리한 인터페이스를 제공해주는 것이다. <br> 이 퍼사드 패턴은 제 3의 API(Third Party API)같은 외부 라이브러리를 추상화 하는데도 사용되기도 한다. 

클라이언트는 퍼사드에서 고수준 인터페이스를 정의하기 때문에 서브시스템을 더 쉽게 사용할 수 있고 오직 퍼사드만 알아도 되므로 서브시스템에 의존하지 않을 수 있게 된다.

### Facade Pattern 예시

<p align="center"><img width="70%" alt="99B6F54A5C68D4A91D" src="https://user-images.githubusercontent.com/51703260/136668628-26a61237-872a-49a7-b053-cdfaad07cb46.jpg"></p>

전자레인지를 예시로 퍼사드 패턴을 설명해보려고 한다.
   
전자레인지를 작동시키는 방법은 전원을 연결시키고 타이머를 설정하고 버튼을 눌러 작동 시킬 수 있다.
   
우리가 전자레인지를 사용하기 위해서는 전자레인지가 동작하는 원리라던가, 복잡한 내부구조에 대해서는 굳이 알 필요가 없다.
   
이런것이 일종의 전자레인지 퍼사드라고 이해해도 좋을 것 같다.만
   
#### 전자레인지의 내부 구성
- `스위치` : 전원을 키고 끔
- `쿨러` : 전자레인지를 식혀줌
- `턴테이블` :  회전시킴
- `마그네트론` : 마이크로파를 발생시킴
- `타이머` : 일정 시간동안 전자레인지를 작동시킴

> MicrowaveSwitch.java
```java
public interfaces MicrowaveSwitch{
     public void on();
     public void off();
}
```

> MicrowaveCooler.java
```java
public class MicrowaveCooler implements MicrowaveSwitch {
    @Override
    public void on() {
        System.out.println("Cooler Start");
    }
    
    @Override
    public void off() {
        System.out.println("Cooler Stop");
    }
}
```

> MicrowaveTurntable.java
```java
public class MicrowaveTurntable implements MicrowaveSwitch{
    @Override
    public void on() {
        System.out.println("Turntable Start");
    }
    
    @Override
    public void off() {
        System.out.println("Turntable Stop");
    }
}
```

> MicrowaveMagnetron.java
```java
public class MicrowaveMagnetron implements MicrowaveSwitch {
    @Override
    public void on() {
        System.out.println("Magnetron Start");
    }
 
    @Override
    public void off() {
        System.out.println("Magnetron Stop");
    }
}
```

> MicrowaveTimer.java
```java
import java.util.Timer;
import java.util.TimerTask;

public class MicrowaveTimer implements MicrowaveSwitch{
    public static long TIME_INTERVAL = 1000;
    private final int EXPIRED_TIME;
    private Timer timer;
    private TimerTask task;
    MicrowaveFacade microwave;
    int count = 0;
    
    public MicrowaveTimer(int sec, MicrowaveFacade microwave) {
        super();
        this.EXPIRED_TIME = sec;
        this.count = EXPIRED_TIME;
        this.microwave = microwave;
        timer = new Timer();
        task = new TimerTask() {
            @Override
            public void run() {
                if(count > 0) System.out.println("Timer : " + (count--) + " sec");
                else {
                    System.out.println("Timer End");
                    timer.cancel();
                    microwave.off();
                }
            }
        };
    }
 
    @Override
    public void on() {
        System.out.println("Timer Start" );
        timer.schedule(task, 0, TIME_INTERVAL);
    }
    
    @Override
    public void off() {
        timer.cancel();
    }
}
```

만약 우리가 전자레인지를 퍼사드 패턴을 쓰지 않고 작동시킨다면, 우리는 직접 모든 내부 장치들의 스위치를 키고 꺼야한다.

먼저 쿨러를 키고, 마그네트론을 키고 턴테이블을 돌린 다음에 타이머를 켜서 원하는 시간만큼 작동시킨다. 그리고 작동이 끝나거나 정지시키려면 역순으로 하나씩 모두 직접 스위치를 내려 꺼야한다.

하지만 아래와 같이 우리가 일상에서 사용하는 전자레인지는 아래와 같이 퍼사드 패턴을 적용시켜서 내부 구조를 알지 못해도 그냥 버튼을 하나만 눌러도 전자레인지의 온전한 기능을 모두 누릴 수 있게된다.

그리고 각각의 부품을 다른 부품으로 교체(700와트 -> 1000와트)하여도 사용자들은 바뀐 부품에 따라 다르게 전자레인지를 작동시키는 것이 아닌 예전과 동일하게 전자레인지를 사용할 수 있기 때문에 퍼사드 패턴을 적용하면 클라이언트가 서브시스템에 의존하지 않을 수 있게 된다는 것이다.

> MicrowaveFacade.java
```java
public class MicrowaveFacade {
    MicrowaveCooler cooler;
    MicrowaveMagnetron magnetron;
    MicrowaveTimer timer;
    MicrowaveTurntable turntable;
    MicrowaveSwitch[] switches;
    boolean isActive = false;
    
    public MicrowaveFacade(MicrowaveCooler cooler, MicrowaveMagnetron magnetron, MicrowaveTimer timer, MicrowaveTurntable turntable) {
        super();
        this.cooler = cooler;
        this.turntable = turntable;
        this.magnetron = magnetron;
        this.timer = timer;
        switches = new MicrowaveSwitch[]{cooler, turntable,  magnetron, timer};
    }
 
    public MicrowaveFacade(int time) {
        super();
        cooler = new MicrowaveCooler();
        turntable = new MicrowaveTurntable();
        magnetron = new MicrowaveMagnetron();
        timer = new MicrowaveTimer(time, this);
        switches = new MicrowaveSwitch[]{cooler, turntable,  magnetron, timer};
    }
    
    public void on() {
        System.out.println("Microwave On");
        for(int i=0; i<switches.length; ++i) {
            switches[i].on();
        }
        isActive = true;
    }
    
    public void off() {
        for(int i=switches.length-1; i>=0; i--) {
            switches[i].off();
        }
        System.out.println("Microwave Off");
        isActive = false;
    }
}
```

> MicrowaveTest.java
```java
public class MicrowaveTest {
    public static void main(String[] args) {
        MicrowaveFacade microwave = new MicrowaveFacade(10);
        microwave.on();
    }
}
```

위 코드를 테스트 해보면, 그냥 전자레인지 타이머를 10초 설정을 하고 그냥 on 하기만 하면 아래와 같은 결과를 확인할 수 있다.

![화면 캡처 2021-10-10 021733](https://user-images.githubusercontent.com/51703260/136668170-ab351727-bba3-480b-b190-2296e4fe9d0a.png)

---

![997F75335C334C3E1E](https://user-images.githubusercontent.com/51703260/136668218-3ce19bb0-4e7a-47d4-be2c-ee8b2f4d61dc.jpg)

위 클래스 다이어그램과 같이 전자레인지를 사용하는 MicrowaveTest(User) 클래스에서는 전자레인지의 내부 부품들이 MicrowaveFacade 클래스에 감싸져 있지만 제공되는 인터페이스(on, off 버튼 등..)를 통해 간편하게 사용할 수 있다.

퍼사드 패턴은 퍼사드 클래스가 서브시스템 클래스들을 캡슐화를 해주는 기능을 제공하는 것 보다, 서브시스템 기능들을 편리하게 사용할 수 있는 인터페이스를 제공하는 것이 주된 목적이다.

# Design Pattern

### Design Pattern이란? 
디자인 패턴은 주로 객체 지향 프로그래밍 설계 설계에서 공통적으로 발생하는 문제를 피하기 위해 자주 쓰이는 설계 방법을 패턴화한 것이다.
디자인 패턴은 의사소통 수단이 될 수 있고, 이를 참고하여 개발할 경우 개발의 효율성, 유지보수성, 운용성, 성능 최적화에 유리하다.

크게 생성, 구조, 행위 3가지 패턴으로 디자인 패턴을 구분지을 수 있다.

**1. 생성 패턴**
   - Builder
   - Prototype
   - [Factory Method](#factory-method)
   - [Abstract Factory](#abstract-factory)
   - [Singleton](#singleton)
   
**2. 구조 패턴**
   - Bridge
   - [Decorator](#decorator)
   - Facade
   - Flyweight
   - Proxy
   - Composite
   - [Adapter](#adapter)
   
**3. 행위 패턴**
   - Interpreter
   - Template Method
   - Chain of Responsibillity
   - Command
   - Iterator
   - Mediator
   - Memento
   - Observer
   - State
   - Strategy
   - Visitor
   
# 1. 생성패턴 
## Factory Method
#### 팩토리 메소드 패턴(Factory Method Pattern)이란 상위 클래스에 알려지지 않은 구현 클래스를 생성하는 패턴이다.
#### 또한 하위 클래스가 어떤 객체를 생성할지 결정하도록 하는 패턴이기도 하다. 그리고 상위 클래스 코드에 구체적인 클래스 이름을 감추기 위한 방법으로도 사용한다.

<p align="center"><img src="./img/factory1.gif"></p>

팩토리는 공장이란 뜻이다. 따라서 팩토리 메소드 패턴도 무언가를 위한 공장이라고 보면 된다.

팩토리 메소드 패턴을 이해하기 위해서는 먼저 아래와 코드와 같은 신발 매장 예시를 살펴보자.

```java
// 이름을 통해 신발을 주문 받음
Shoes orderShoes(String name) {
    // 해당 이름의 신발을 찾아서 특정 구상 객체 생성
    Shoes shoes;
    
    if (name.equals("blackShoes"))     shoes = new BlackShoes();
    else if (name.equals("brownShoes"))shoes = new BrownShoes();
    else if (name.equals("redShoes"))  shoes = new RedShoes();
    
    // 신발을 준비하고 포장하는 메소드
    // 모든 신발 공용 메소드
    shoes.prepare(); 
    shoes.packing(); 
 
    return shoes;
}
```
현재 이 신발 매장에는 3개의 신발만 팔고 있다. 그리고 앞으로 판매되는 제품이 늘어나거나 지금 있는 제품이 더 이상 판매 되지 않을 수도 있다. 이 부분은 언제나 변경이 가능 한 부분이다.

그러나 밑에 있는 prepare()과 packing() 두 메소드는 제품에 변화가 생기더라도 변하지 않는 부분이다.

위 코드를 간단하게 캡슐화하여 ShoesFactory라는 클래스로 만들면

> ShoesFactory.java

```java
public class ShoesFactory {
 
    public Shoes makeShoes(String name) {
 
       Shoes shoes = null;
       if (name.equals("blackShoes"))     shoes = new BlackShoes();
       else if (name.equals("brownShoes"))shoes = new BrownShoes();
       else if (name.equals("redShoes"))  shoes = new RedShoes();
       
       return shoes;
    }
 
}
```

위 코드처럼 만들 수 있다. 그리고 사용은 아래처럼 할 수 있다.

> ShoesStore.java

```java
public class ShoesStore {
     
    ShoesFactory factory;
 
    public ShoesStore(ShoesFactory factory) {
        this.factory = factory;
    }
   
    Shoes orderShoes(String name) {
        Shoes shoes = factory.makeShoes(name);
        shoes.prepare(); 
        shoes.packing(); 
 
        return shoes;
    }
}
```

고객에게 특정 신발에 대한 주문이 들어 왔을 때 매장에서는 공장에 해당 신발 오더를 넣고 받으면 되고, 판매하는 신발이 늘어나거나 단종되면 신발 매장이 아닌 신발 공장에서 그 변화를 처리할 수 있다.

위의 예시 코드는, 디자인 패턴까지는 아니고 프로그래밍에 사용하는 관용구정도로 보면 된다.

그렇다면 이제 본격적으로 팩토리 메소드 패턴을 살펴보자. 

위에서 봤던 신반 매장은 점점 성장하여 다른 나라로 진출하기 시작했다. 일본과 프랑스에도 진출을 하여 매장을 지었다고 하자.

```java
   JapanShoesStore jpStore= new JapanShoeStore (new JapanShoesFactory());
   jpStore.order("blackShoes");
    
   FranceShoesStore  frStore = new FranceShoesStore (new FranceShoesFactory());
   frStore.order("blackShoes");
```

그런데 이 해외 매장들이 본사에서 준 가이드라인 그대로 똑같이 만들지 않고, 현지 상황에 맞춰 일본에서는 약간 굽을 높게 만들고 프랑스에서는 신발 옆에 패턴을 넣기 시작했다. 
뿐 만 아니라 포장까지도 자기 마음대로 하였다.

그래서 본사는 매장과 신발 생산 과정 전체를 묶어주는 아래와 같은 프레임 워크를 만들어 모든 매장에서 이를 따르게 하였다.

> ShoesStore.java

```java
   public abstract class ShoesStore {
 
    public ShoesStore orderShoes(String name) {
        Shoes shoes = makeShoes(name);
        shoes.prepare();
        shoes.packing();
 
        return shoes;
 
    }
 
    abstract Shoes makeShoes(String name);
 
}
```
ShoesStore 추상 클래스를 선언하면, 달라지는 부분은 추상메소드인 신발 제작 뿐이다. 

각 현지 상황메 맞춰 makeShoes 메소드를 오버라이드하여 일본에서는 약간 굽을 높게 만들고, 프랑스에서는 패턴을 넣어 신발을 제작하면 된다.

그대신 제작, 준비, 포장하는 공정은 ShoesStore를 상속하는 전 세계 모든 매장들에서 똑같은 시스템이 적용 될 수 있다.

> JapanShoesStore.java

```java
class JapanShoesStore extends ShoesStore {
 
    @Override
    Shoes makeShoes(String name) {
        if (name.equals("blackShoes")) return new JPStyleBlackShoes();  
        else if (name.equals("brownShoes")) return new JPStyleBrownShoes();
        else if (name.equals("redShoes")) return new JPStyleRedShoes();
        else return null;
   }

}
```

> FranceShoesStore.java

```java
class FranceShoesStore extends ShoesStore {
 
    @Override
    Shoes makeShoes(String name) {
        if (name.equals("blackShoes")) return new FRStyleBlackShoes();  
        else if (name.equals("brownShoes")) return new FRStyleBrownShoes();
        else if (name.equals("redShoes")) return new FRStyleRedShoes();
        else return null;
   }

}
```

여기서 가장 중요한 점은 하위 클래스에서 메소드를 오버라이딩 하였기 때문에, 슈퍼클래스에 있는 orderShoes 메소드에서는 어떤 신발이 만들어 지는지 전혀 모르고 있다는 것이다. 
동적 바인딩되는 그 메소드에서 주는 신발을 받아서 준비하고 포장할 뿐 이다.

<p align="center"><img src="./img/factory3.png"></p>

#### 신발을 주문받고 생산하는 생산자 클래스

<p align="center"><img src="./img/factory4.png"></p>

#### 생산자 클래스에서 생산되는 제품 클래스

팩토리 메소드 패턴의 클래스들은 크게 생산자 클래스와 제품 클래스로 구분할 수 있다.

이제 생산자 클래스인 ShoesStore 클래스에서 사용될 제품 클래스 Shoes 클래스를 작성해 보자.

> Shoes.java

```java
abstract class Shoes {
 
    String name;
    String bottom;
    String leather;
    boolean hasPattern;
 
    void prepare() {
        System.out.println("주문하신 신발을 준비중입니다.");
    }
 
    void packing() {
        System.out.println("준비 완료된 신발을 포장중입니다.");
    }
 
    public String getName() {
        return name;
    }
 
}
```

> JPStyleBlackShoes.java

```java
class JPStyleBlackShoes extends Shoes {
 
    public JPStyleBlackShoes() { 
        name = "일본 스타일의 검은 구두";
        bottom = "굽이 높은 밑창";
        leather = "스웨이드";
        hasPattern = false;
    }
 
}

```

> FRStyleBlackShoes.java

```java
class FRStyleBlackShoes extends Shoes {
 
    public FRStyleBlackShoes() { 
        name = "프랑스 스타일의 검은 구두";
        bottom = "일반 굽높이 밑창";
        leather = "스웨이드";
        hasPattern = true;
    }
 
}
```

추상 클래스로 Shoes 클래스를 설계하고, JPStyleBlackShoes, FRStyleBlackShoes에서 멤버변수를 초기화하며 구현해주었다.

> __추상 메소드가 없는 추상 클래스가 여기서 등장한다.__
> Shoes 클래스는 추상 메소드가 존재하지 않지만 추상 클래스로 선언되었기 때문에 new Shoes();로 직접 객체 생성은 불가능하고,
> Shoes 클래스를 상속받는 클래스에서 Shoes를 참조변수로하여 객체 생성을 할 수 있다.
> 추상 메소드 존재 O -> 무조건 클래스도 추상 클래스로 선언
> 추상 메소드 존재 X -> 현재 클래스로 다이렉트 객체 생성을 막고 싶을 때, 추상 클래스로 선언

여기까지 UML에 설계된 클래스들의 구현이 마무리 되었다. 
이제 메인 클래스에서 위에서 설계한 팩토리 메소드 패턴이 어떻게 사용되는지 살펴 보자.

> Main.java

```java
public class Main {
 
    public static void main(String[] args) {
        
        // 일본과 프랑스에 현지 트렌드에 맞춰 매장을 열었음
        ShoesStore jpStore = new JapanShoesStore();
        ShoesStore frStore = new FranceShoesStore();
      
        // 일본 매장에서 검은 신발 주문
        Shoes shoes = jpStore.orderShoes("blackShoes");
        System.out.println("일본 매장에서 주문한 검은 신발 : " + shoes.getName());
        
        System.out.println();
        
        // 프랑스 매장에서 검은 신발 주문
        shoes = frStore.orderShoes("blackShoes");
        System.out.println("프랑스 매장에서 주문한 검은 신발  : " + shoes.getName());
 
    }
 
}

위 코드를 실행해보면 아래와 같이 출력될 것이다.

```text
> 주문하신 신발을 준비중입니다.
> 준비 완료된 신발을 포장중입니다.
> 일본 매장에서 주문한 검은 신발 : 일본 스타일의 검은 구두
> 
> 주문하신 신발을 준비중입니다.
> 준비 완료된 신발을 포장중입니다.
> 프랑스 매장에서 주문한 검은 신발 : 프랑스 스타일의 검은 구두
```

위 메인 메소드의 프로세스는 아래와 같다. 참고로 일본 매장과 프랑스 매장에서의 프로세스는 동일하니 공통적인 프로세스로 묶어서 설명하겠다.

1. 현지 신발 매장이 문을 열었음. (ShoesStore를 참조변수로 하는 현지 ShoesStore 객체 생성)
2. 매장에 신발 종류를 통해 신발을 주문함. (ShoesStore의 orderShoes메소드)
3. ShoesStore의 orderShoes 내부에서 생성된 객체에 맞게 동적바인딩되어 오버라이딩된 makeShoes 메소드가 실행됨
4. 오버라이딩된 makeShoes 메소드에서 주문에 맞는 신발객체를 호출된 orderShoes메소드로 리턴함
5. prepare(), packing() 메소드가 실행됨
6. make, prepare, packing이 완료된 Shoes 객체를 리턴함
7. 주문한 신발이 어떤 객체인지 출력하여 확인

마무리로 다시 한번 팩토리 메소드 패턴을 정리하자면, 팩토리 메소드 패턴은 객체를 만들어내는 부분을 자식 클래스에 위임하는 패턴이다.

new 키워드를 호출하는 부분을 서브 클래스에 위임하였기 때문에, 상위 클래스인 ShoesStore 클래스 내부에는 new 라는 키워드가 존재하지 않는다. 

즉, 상위 클래스가 아닌 하위 클래스에서 어떤 클래스를 만들지 결정하게 하도록 하는 것이다. 

하위 클래스에서 추상 메소드인 makeShoes메소드를 오버라이딩 하였기 때문에, 상위 클래스에 있는 orderShoes 메소드에서는 어떤 신발이 만들어 지는지 전혀 모르고 있다.

동적 바인딩된 그 메소드에서 주는 신발을 받아서 준비하고 포장해서 내놓을 뿐 이다.

> 객체 지향 프로그래밍 세계에서 자식은 부모를 알아도, 부모는 자식을 모른다. 

---

## Abstract Factory
#### 추상 팩토리 패턴(Abstract-Factory Pattern)이란 인터페이스를 이용하여 서로 연관된, 또는 의존하는 객체를 구현 클래스를 지정 하지 않고도 생성할 수 있는 패턴이다.
#### 바로 위 팩토리 메소드 편에 보았던 JPStyleBrownShoes, FRStyleRedShoes와 같이 추상 클래스에 의존 하는 구현 클래스를 만들지 않고도 생성할 수 있다. 

```java
class DependentShoesStore {
 
    public Shoes makeShoes(String style, String name) {
        Shoes shoes = null;
        if (style.equals("Japan")) {
            if (name.equals("blackShoes")) shoes = new JPStyleBlackShoes();
            else if (name.equals("brownShoes")) shoes = new JPStyleBrownShoes();
            else if(name.equals("redShoes")) shoes = new JPStyleRedShoes();
        }
        else if(style.equals("france")) {  
            if (name.equals("blackShoes")) shoes = new FRStyleBlackShoes();
            else if (name.equals("brownShoes")) shoes = new FRStyleBrownShoes();
            else if(name.equals("redShoes")) shoes = new FRStyleRedShoes(); 
        }
        shoes.prepare();
        shoes.packing();
        return shoes;
    }

}
```
만약 위와 같이 스타일과 신발 이름을 입력받아 해당 신발을 제작하고 준비, 포장해서 돌려주는 클래스가 있다고 하자.

직전 팩토리 메소드 패턴을 정리할 때 이미 한번 생각해본 적이 있었는데, 

<p align="center"><img src="./img/AbFactory2.png"></p>

지금 코드는 몇 줄 되지 않는데도 이와 같이 복잡하고 관리 하기 힘든 모습인데, 만약 나라가 수십개국에 신발종류도 각 나라마다 무수히 많다면 어떻게 될까??

그리고 나중에 이것들을 수정해야 할 일이 생긴다면 정말 생각만 해도 끔찍하다...

구두를 만드는 스토어 객체는 스토어 객체는 구두 객체들을 가지고 있으면서, 이 객체들을 사용해서 구두를 준비하고, 포장하게 된다.

이때 스토어 객체는 고수준 컴포넌트라고 하고, 구두 객체들을 저수준 컴포넌트라고 한다. 고수준 컴포넌트(스토어)는 저수준 컴포넌트(구두들)를 가지고 사용 할 수 있다.

그래서 위에 있는 다이어그램을 보면, 고수준의 컴포넌트가 저수준의 컴포넌트에 심하게 의존한다는 것을 볼 수 있다.

의존한다는 것은 나중에 새로운 구두가 추가 되면, 스토어 객체까지 손봐야 할 일이 생긴다는 의미라서 이 의존관계를 뒤집을 필요가 있다.

그래서 위 설계는 객체지향 설계 5대 원칙 [SOLID](../../../tree/main/CommonSense#oop의-5가지-설계-원칙)중, 5번째 [DIP](../../../tree/main/CommonSense#dip)를 따르는 설계가 필요하다.

> DIP (Dependency-Inversion Principle) : 구상 클래스에 의존하도록 만들지 않고, 추상화 된 것에 의존하도록 만들어야 한다.

이 원칙을 제대로 적용하려면, 구현 클래스처럼 구체적인 것이 아니라 추상 클래스나 인터페이스같이 추상적인 것에 의존 하는 코드를 만들어서 고수준 컴포넌트와 저수준 컴포넌트 모두에 적용하여야 한다.

그래서 방금 말한 의존관계 역전 원칙을 구두 가게에 다시 적용해보자면 아래와 같은 UML처럼 설계가 가능하다.

<p align="center"><img src="./img/AbFactory3.png"></p>

이렇게 하면 고수준 컴포넌트 ShoesStore와 저수준 컴포넌트인 각종 구두 객체들 모두 추상클래스인 Shoes에 의존 하게 된다. 

하지만 설계를 하다보면 의존관계 역전 원칙을 지키도록 설계하기가 쉽지 않다. 

그래서 의존 관계 역전원칙을 지키는데 도움이 될만한 가이드 라인을 가져와봤다.

> 1. 어떤 변수라도 구상 클래스에 대한 레퍼런스를 저장하지 말것
> - new 연산자 사용 하면 구상 클래스 레퍼런스를 저장하는 것, 이것 대신 팩토리를 사용하라!
> 
> 2. 구상 클래스에서 유도된 클래스를 만들지 말 것.
> - 구상 클래스에서 유도 된 클래스를 만들면 특정 구상 클래스에 의존 하게 된다.
> 
> 3. 베이스 클래스에 이미 구현되어 있던 메소드를 오버라이드 하지 말 것. 
> - 이미 구현되어 있는 메소드를 오버라이드 하는 것은, 애초부터 베이스 클래스가 잘 추상화 되어 있는 것이 아니다!
> - 베이스 클래스에서 메소드를 정의 할때는 모든 서브클래스에서 공유할 수 있는 것들만 정의 해야 함.

하지만 위 가이드 라인들은 지향하면 좋다는 것이고, 꼭 지켜져야 하는 것은 아니라고 한다. 

실제로 자바 프로그램 가운데 이것을 완벽하게 지키는 것은 거의 없다. 

위와 같이 설계하는 것이 바람직하다는 것을 알고 넘어가면 좋을 것 같다.

<br>
다시 구두 가게로 돌아와서, 우려했던 대로 신발매장이 더 많은 나라에 진출해서 인도, 이태리, 중국 등등 많은 곳에 매장이 생겼다고 가정해보자.

일단 팩토리 메소드 패턴을 이용해 프레임워크를 잘 잡아 놓았기 때문에, 나라별로 같은 서비스를 제공 할 수는 있다.

하지만 몇몇 분점에서는 각 현지 공장에서 싸구려 재료들을 몰래 사용해서 본사에서 의도하지 않은 마진을 몰해 올리고 있다는 소식을 듣고 무언가 조치를 취하려고 한다.

그래서 본사에서 원재료를 사용해서 신발을 만들어서 분점으로 배송하려고 했는데 지난 팩토리 메소드를 정리한 부분에서 먼저 보았듯이, 같은 검은 구두라고 하더라도 일본매장의 검은 신발과 프랑스 매장의 검은 신발의 밑창은 서로 다르게 만들어야하고 따라서 재료들도 달라져서 문제가 생긴다.

그래서 다시 생각해낸 해결 방법이 지역 별로 소규모 신발재료 공장을 나누어서 신발을 만드는 것이다.

```java
interface ShoesIngredientFactory {
 
    public Bottom makeBottom();
    
    public Leather makeLeather();
 
    public boolean hasPattern();
 
}
```
그래서 위와 같은 공통 기능을 제공할 신발재료 공장 인터페이스를 만들어 주었다.

> JPShoesIngredientFactory.class

```java
class JPShoesIngredientFactory implements ShoesIngredientFactory {
 
    @Override
    public Bottom makeBottom() return new RubberBottom();
 
    @Override
    public Leather makeLeather() return new LeatherOfCows();
    
    @Override
    public boolean hasPattern() return false;
 
}
```

> FRShoesIngredientFactory.class

```java
class FRShoesIngredientFactory implements ShoesIngredientFactory {
 
    @Override
    public Bottom makeBottom() return new PlasticAndRubberBottom();
 
    @Override
    public Leather makeLeather() return new LeatherOfSheeps();
 
    @Override
    public boolean hasPattern() return true;
 
}
```

이번에도 지난 팩토리 메소드때와 동일하게 일본과 프랑스를 기준으로 설명하려고 한다.

일본 매장으로 가는 신발재료 공장 클래스와 프랑스 매장으로 가는 신발재료 공장 클래스 처럼 재료 공장 인터페이스를 구현하는 클래스를 만들었다.

그리고 공장에서 각 메소드들이 return 해주는 각가의 신발 재료들이 구현해야하는 인터페이스는 아래와 같다.

```java
interface Bottom {
 
    public String getName();
 
}
```

```java
interface Leather {
 
    public String getName();
 
}
```

위 재료 인터페이스를 구현한 클래는 아래와 같다. 

> RubberBottom.class

```java
// 고무 밑창
class RubberBottom implements Bottom {
 
    @Override
    public String getName() return "고무";
 
}
```

> PlasticAndRubberBottom.class

```java
// 플라스틱과 고무 혼한 밑창
class PlasticAndRubberBottom implements Bottom {
 
    @Override
    public String getName() return "플라스틱, 고무";
 
}
```

> LeatherOfCows.class

```java 
// 소가죽
class LeatherOfCows implements Leather {
 
    @Override
    public String getName() return "소가죽";
 
}
```

> LeatherOfSheeps.class

```java 
// 양가죽
class LeatherOfSheeps implements Leather {
 
    @Override
    public String getName() return "양가죽";
 
}
```

이제는 공장은 완성됐고, 공장에서 만드는 신발 클래스를 살펴보도록 하자.

> Shoes.class

```java
abstract class Shoes {
 
    String name;
    Bottom bottom;
    Leather leather;
    boolean hasPattern;
 
    abstract void assembling(); // 신발을 조립하는 추상 메소드
 
    void prepare() {
        System.out.println("완성된 신발을 준비 중입니다.");
    }
 
    void packing() {
        System.out.println("준비된 신발을 포장 중입니다.");
    }
 
    public String getName(){
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
}
```

위 Shoes 클래스에서 주목할 점은, 원재료 들을 조립하는 assembling 이라는 추상 메소드이다.

```java
abstract class Shoes {
 
    String name;
    String bottom;
    String leather;
    boolean hasPattern;
 
    void prepare() {
        System.out.println("주문하신 신발을 준비중입니다.");
    }
 
    void packing() {
        System.out.println("준비 완료된 신발을 포장중입니다.");
    }
 
    public String getName() {
        return name;
    }
 
}
```

지난번 팩토리 메소드에서 사용했던 Shoes.class에서는 존재하지 않는 메소드이다.

> BlackShoes.class

```java
class BlackShoes extends Shoes {
 
    ShoesIngredientFactory shoesIngredientFactory;
 
    public BlackShoes(factory_abstract_factory.ShoesIngredientFactory shoesIngredientFactory) {
        this.shoesIngredientFactory = shoesIngredientFactory;
    }
 
    @Override
    void assembling() { 
        System.out.println("신발을 제작중입니다. " + name);
        leather = shoesIngredientFactory.makeLeather();
        bottom = shoesIngredientFactory.makeBottom();
        System.out.println("신발 정보 : 밑창은 " + bottom.getName() + " 사용 하였으며, 가죽은 " + leather.getName() + " 사용하였습니다.");
    }
 
}
```

> BrownShoes.class

```java 
class BrownShoes extends Shoes {
 
    ShoesIngredientFactory shoesIngredientFactory;
 
    public BrownShoes(factory_abstract_factory.ShoesIngredientFactory shoesIngredientFactory) {
        this.shoesIngredientFactory = shoesIngredientFactory;
    }
 
    @Override
    void assembling() {
        System.out.println("신발을 제작중입니다. " + name);
        leather = shoesIngredientFactory.makeLeather();
        bottom = shoesIngredientFactory.makeBottom();
        System.out.println("신발 정보 : 밑창은 " + bottom.getName() + " 사용 하였으며, 가죽은 " + leather.getName() + " 사용하였습니다.");
    }
 
}
```

> RedShoes.class

```java 
class RedShoes extends Shoes {
 
    ShoesIngredientFactory shoesIngredientFactory;
 
    public RedShoes(factory_abstract_factory.ShoesIngredientFactory shoesIngredientFactory) {
        this.shoesIngredientFactory = shoesIngredientFactory;
    }
 
    @Override
    void assembling() { 
        System.out.println("신발을 제작중입니다. " + name);
        leather = shoesIngredientFactory.makeLeather();
        bottom = shoesIngredientFactory.makeBottom();
        System.out.println("신발 정보 : 밑창은 " + bottom.getName() + " 사용 하였으며, 가죽은 " + leather.getName() + " 사용하였습니다.");
    }
 
}
```

위 클래스들은 보다시피 Shoes 추상 클래스를 구현한 각 컬러들의 Shoes 클래스이다. 

이제는 더 이상 각 국가별로 BlackShoes, BrownShoes, RedShoes 각각 다 만들어주지 않아도 된다.

이 클래스들은 ShoesIngredientFactory 인스턴스를 생성자로 받아서 이 인스턴스로부터 원재료를 직접 받게된다. 

추상 메소드여서 오버라이딩하여 구현해준 assembling 메소드를 보면 가죽과 밑창을 각각 공장 인스턴스에서 받아 조립하고 있음을 볼 수 있다.

여기에서 주목할점은 Shoes 클래스는 그냥 공장에서 건네주는 재료로 신발을 조립만하기 때문에, 어떤 지역의 팩토리를 사용하든 Shoes 클래스는 언제든 재활용 할 수 있다는 것이다. 

<br>
> ShoesStore.class

```java
abstract class ShoesStore {
 
    public Shoes orderShoes(String name) {
 
        Shoes shoes;
 
        shoes = makeShoes(name);
        shoes.assembling();
        shoes.prepare();
        shoes.packing();
 
        return shoes;
 
    }
 
    abstract Shoes makeShoes(String name);
 
}
```
고객에게 주문을 받을 수 있는 Store 클래스를 만들어 보았다.

각 나라의 스토어들은 이 Store 추상 클래스를 상속받아 추상 메소드인 makeShoes 메소드를 각 나라에 맞게 오버라이드하여 구현해주면 된다.

orderShoes는 전세계 공통 프레임워크이고, orderShoes안에 있는 makeShoes 단계만 각 나라의 특징에 맞게 바뀌는 것 뿐이다.

> JPShoesStore.class

```java
class JPShoesStore extends ShoesStore {
 
    @Override
    Shoes makeShoes(String name) { 
        Shoes shoes = null;
        ShoesIngredientFactory shoesIngredientFactory = new JPShoesIngredientFactory();
        
        if(name.equals("blackShoes")) {
            shoes = new BlackShoes(shoesIngredientFactory);
            shoes.setName("일본 스타일의 검은 신발");
        }
        else if(name.equals("brownShoes")) {
            shoes = new BrownShoes(shoesIngredientFactory);
            shoes.setName("일본 스타일의 갈색 신발");
        }
        else if (name.equals("redShoes")) {
            shoes = new RedShoes(shoesIngredientFactory);
            shoes.setName("일본 스타일의 빨간 신발");
        }
 
        return shoes;
    }
 
}
```

> FRShoesStore.class

```java
class FRShoesStore extends ShoesStore {
 
    @Override
    Shoes makeShoes(String name) { 
        Shoes shoes = null;
        ShoesIngredientFactory shoesIngredientFactory = new FRShoesIngredientFactory();
        
        if(name.equals("blackShoes")) {
            shoes = new BlackShoes(shoesIngredientFactory);
            shoes.setName("프랑스 스타일의 검은 신발");
        }
        else if(name.equals("brownShoes")) {
            shoes = new BrownShoes(shoesIngredientFactory);
            shoes.setName("프랑스 스타일의 갈색 신발");
        }
        else if(name.equals("redShoes")) {
            shoes = new RedShoes(shoesIngredientFactory);
            shoes.setName("프랑스 스타일의 빨간 신발");
        }
 
        return shoes;
    }
 
}

```
일본과 프랑스 신발 매장을 ShoesStore클래스를 상속받아 만들어 주었다.

각 매장은 makeShoes를 오버라이딩 하여 현지 상황에 맞게 재정의한다. 

makeShoes내부 프로세스를 살펴보면, 먼저 매장으로 신발 주문이 들어오면 현지 공장 인스턴스를 생성한다. 

그리고 매장에서 신발 재료 팩토리 인스턴스를 보내서 원재료 공장으로부터 재료들를 모두 받아오는 것이다. 

makeShoes 메소드에서 재료를 공장에서 모두 받아왔다면, 이제는 매장에서 받은 재료를 가지고 조립하고, 준비, 포장하여 작업을 마무리 하는 것이다.

위 과정이 전세계 공통 orderShoes 프레임워크이다.

이제는 신발공장과 매장, 신발까지 모든 설계가 마무리 되었다.

실제 주문을 하는 과정을 살펴 보며 이번 추상 팩토리 패턴을 마무리하려고 한다.

> Main.class

```java
public class Main {
 
    public static void main(String[] args) {
 
        JPShoesStore jpStore = new JPShoesStore();
        jpStore.orderShoes("blackShoes");
 
        FRShoesStore frStore = new FRShoesStore();
        frStore.orderShoes("redShoes");
 
    }
 
}
```

위 주문 코드를 실행해보면 출력은 아래와 같을 것이다.
```
> 신발을 제작중입니다. 일본 스타일의 검은 신발
> 신발 정보 : 밑창은 고무 사용 하였으며, 가죽은 소가죽 사용하였습니다.
> 완성된 신발을 준비 중입니다.
> 준비된 신발을 포장 중입니다.
> 신발을 제작중입니다. 프랑스 스타일의 검은 신발
> 신발 정보 : 밑창은 플라스틱, 고무 사용 하였으며, 가죽은 양가죽 사용하였습니다.
> 완성된 신발을 준비 중입니다.
> 준비된 신발을 포장 중입니다.
```
일본 매장과 프랑스 매장으로 가서 신발를 주문을 한다고 하자.

1. 먼저 주문하는 일본 매장에서 검은 신발을 주문하면, 매장에서는 주문을 받고 (orderShoes) 

2. 주문을 받은 직원은 일본 매장을 담당하는 원재료 공장에 해당 신발에 알맞는 재료를 요청한다. (makingShoes)

3. 원재료 공장이 가동되고, 구두의 재료를 제작하여 보내준다.

4. 매장에서 재료들을 받아서 조립을 해서 구두를 만든다.

5. 준비, 포장을 해서 고객들에게 제공한다.

6. 프랑스 매장도 마찬가지로 일본 매장과 완전히 동일한 프로세스를 거쳐 고객에게 신발을 제공한다.

<p align="center"><img src="./img/AbFactory1.png"></p>

마지막으로 위 이미지를 그동안 예시를 들었던 신발 매장 패턴을 대입하여 추상 팩토리 패턴을 이해하고 정리하면 좋을 것같다.

결과적으로, 추상 팩토리 패턴을 사용하면 DIP 원칙을 준수하게되어 객체들 간의 결합도 낮아져서 유지 보수가 아주 용이해진다.

---

## Singleton
#### 싱글턴 패턴(Singleton Pattern)이란 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴하는 생성패턴이다. 
- 클래스 내에서 인스턴스가 단 하나뿐임을 보장하므로, 프로그램 전역에서 해당 클래스의 인스턴스를 바로 얻을 수 있고 , 불필요한 메모리 낭비를 최소화한다.
- 키보드 리더, 프린터 스풀러 또는 공통된 객체를 여러개 생성해서 사용하는 DBCP(DataBase Connection Pool) 등 클래스의 객체를 하나만 만들어야 하는 상황에서 사용한다. 
- 싱글톤 패턴을 사용하기 위해서는 반드시 접근제한자를 이용하여 외부의 접근을 막거나, final로 reference를 변경 불가능하게 설정하여야 한다. 

#### Java
```java
public class Singleton1 {

    private static final Singleton1 instance = new Singleton1();

    private Singleton1() {}

    public static Singleton1 getInstance() {
        return instance;
    }
}

```

```java
public class Singleton2 {
    private static Singleton2 instance;

    private Singleton2() {}

    public static Singleton2 getInstance() {
        if (instance == null) {
            instance = new Singleton2();
        }
        return instance;
    }
}
```

# 2. 구조패턴 
## Decorator
#### 데코레이터 패턴(Decorator Pattern)이란 주어진 상황 및 용도에 따라 어떤 객체에 장식(기능)을 추가하는 패턴이다. 
#### 객체에 추가적인 기능을 동적으로 첨가하며, 기능 확장이 필요할 때 서브클래스 대신 쓸 수 있는 유연한 대안이 될 수 있다.

데코레이터 구조패턴은 카페를 예시로 이해하면 쉽게 이해할 수 있다.
카페에서 음료 주문을 받는 프로그램을 객체 지향 프로그래밍 관점으로 만들었다고 가정해 보자.
이 프로그램에서 카페의 음료를 설계한다면 음료가 공통적으로 가지고 있는 성질을 따로 빼서 '음료'라는 클래스로 만들고, 이것을 상속 받아 사용할 것이다.

<p align="center"><img src="./img/Deco1.png"></p>

그래서 위와 같이 가장 상위 클래스인 Beverage 클래스를 생성하고, 카페모카, 바닐라라떼, 아메리카노, 카페라떼 4가지 음료 클래스를 생성하여 Beverage 클래스를 상속받도록 하였다.

그러나 실제 카페에서는 카페모카에 샷추가를 하거나 휘핑크림 추가, 또는 아메리카노에 샷추가를 할 수 있다. 그러한 음료들까지 모두 클래스로 생성을 한다면 아래와 같이 아주 복잡해질 것이다.

<p align="center"><img src="./img/Deco2.png"></p>

보기만해도 복잡하다... 이렇게 된다면 메뉴를 1개만 새로 개발되어도 해당 메뉴의 샷추가, 휘핑추가, 자바칩추가 클래스까지 새로운 파생클래스 수십개가 추가될 것이고, 그렇게 되면 유지보수가 아주 힘들어질 것이다.

그러면 좀 더 개선해서 다양한 옵션들을 Beverage 클래스에 Boolean 타입으로 관리한다고 하자.

<p align="center"><img src="./img/Deco3.png"></p>

Beverage 클래스의 cost 메소드에서 if문으로 hasMilk, hasCream 등을 체크하여 옵션별 가격들을 더할 수 있다.

```java
public int cost() {    
        int total = 0;
        if(hasMilk()) total+=500;
        if(hasShot()) total+=400;
        if(hasCream()) total+=300;
        if(hasJavachip()) total += 700;
        
        return total;
}
```

그리고 Beverage 클래스 상속받는 메뉴들은 아래와 같이 가격을 결정지을 수 있다.

```java
public class Americano extends Beverage {

    @Override
    public int cost() {
        return 5000 + super.cost();
    }
    
}
```

위 예시는 언뜻 보면 적절하게 설계한 것처럼 보일 수 있다.
하지만 2가지 정도의 큰 문제점이 있다.

1. 새로운 옵션이 추가되거나 변경될 때 마다 슈퍼클래스인 Beverage를 고쳐야 한다. 슈퍼클래스의 변경이 잦은것은 절대 좋은 설계가 아니다.

2. Beverage에 모든 옵션을 관리하면 서브클래스에서 쓸데 없는 정보들까지 모두 상속받는다. 만약 자몽셔벗 클래스가 Beverage를 상속받았는데 불필요한 옵션인 샷추가나 휘핑까지 관리하게 되는것도 역시 좋은 설계가 아니다.

또한 hasMilk가 boolean이다 보니 옵션을 2번이상 추가할 수 없다.
그렇기 때문에 total += milk*500 + shot*500 + ...; 처럼 사용할 수 있게 int형으로 변경이 필요해 보인다.

그래서 위 설계는 객체지향 설계 5대 원칙 [SOLID](../../../tree/main/CommonSense#oop의-5가지-설계-원칙)중 특히, 2번째 [OPC](../../../tree/main/CommonSense#ocp)를 완전히 위반한 설계이다.

> OCP (Open-Closed Principle) : 클래스는 확장에 대해서는 열려 있고, 변경에 대해서는 닫혀 있어야 한다.
즉, 새로운 기능을 추가하려 할 때, 기존 슈퍼 클래스는 수정하지 않고 확장을 통해 간단하게 추가 할 수 있도록 한다!

---

그래서 프로그램을 데코레이터 패턴을 적용해 새롭게 설계하려 한다.

<p align="center"><img src="./img/Deco5.png"></p>

데코레이터 패턴의 일반적인 형태는 위와 같다.

<p align="center"><img src="./img/Deco4.png"></p>

그리고 프로그램을 데코레이터 패턴을 적용한 설계는 위와 같다.
데코레이션 중 최상위 클래스인 CondimentDecorator 클래스가 있으며, 모든 추가 옵션들은 CondimentDecorator 클래스를 상속받는다.
그리고 각각의 메뉴 클래스들과 CondimentDecorator 클래스가 Beverage 클래스를 상속받는다.

```java
public abstract class Beverage {

    // 음료 이름
    String description = "Menu Name (No title)";
    // 음료 가격
    public abstract int cost();
    // 옵션들
    public String getDescription() {
        return description;
    }
    
}
```

위는 최상위 클래스인 Beverage 클래스다.
카페에서 판매하는 모든 음료는 이 클래스를 상속 받아야 하고, cost는 추상메소드이므로 하위 클래스에서 반드시 각자의 가격으로 이 메소드를 오버라이딩하여 구현해주어야 한다. 
그렇기 때문에 Beverage 당연히 클래스도 추상클래스가 된다. 

```java
public abstract class CondimentDecorator extends Beverage {
    public abstract String getDescription();
}
```

위는 Beverage클래스를 상속받는 CondimentDecorator 클래스다.
데코레이션의 최상위 클래스로, 추가 옵션들은 모두 CondimentDecorator 클래스를 상속받는다.
CondimentDecorator 클래스는 Beverage 클래스의 getDescription() 메소드를 추상메소드로 오버라이딩하였기 때문에,
CondimentDecorator를 상속받는 옵션들이 이를 반드시 구현 해주어야 한다.
그래서 CondimentDecorator 또한 추상클래스가 되었고, 추상클래스이기 때문에 Beverage 클래스의 추상메소드인 cost()를 여기서 구현해주지 않아도 된다.

> 최상위 클래스를 상속받는 중간 클래스에서 
최상위 클래스의 메소드를 추상메소드로 오버라이딩을 할 수 있다.
최상위클래스에서 없던 의무성을 중간 클래스에서 아래로 부여할 수도 있다.

``` java
public class Americano extends Beverage {
 
    public Americano() {
    	super();
        description = "아메리카노";
    }
 
    @Override
    public int cost() {
        return 3800;
    }
 
}
```

```java
public class CaffeMocha extends Beverage {
 
    public CaffeMocha() {
        super();
        description = "카페모카";
    }
 
    @Override
    public int cost() {
        return 4800;
    }
 
}
```
Beverage 클래스를 상속받는 Americano와 CaffeMocha 클래스이다.
생성자에서 음료의 이름을 지정해주고, 추상메소드였던 cost 메소드를 오버라이딩하여 각자의 음료에 맞는 가격을 정해준다.

```java
public class Whip extends CondimentDecorator {
 
    Beverage beverage;
    
    public Whip(Beverage beverage) {
        super();
        this.beverage = beverage;
    }
 
    @Override
    public String getDescription() {
        return beverage.getDescription() + ", 휘핑크림";
    }
 
    @Override
    public int cost() {
        return beverage.cost() + 500;
    }
    
}
```

```java
public class Shot extends CondimentDecorator {
    
    Beverage beverage;
 
    public Shot(Beverage beverage) {
        super();
        this.beverage = beverage;
    }
 
    @Override
    public String getDescription() {
        return beverage.getDescription() + ", 샷";
    }
 
    @Override
    public int cost() {
        return beverage.cost() + 500;
    }
    
}

```

CondimentDecorator 클래스를 상속받는 Whip과 Shot 클래스이다.
생성자에서 전달받은 Beverage객체의 인스턴스(Americano, CaffeMocha 등 음료)의 멤버와 메소드에 접근하여 추상메소드들을 오버라이딩한다.

CondimentDecorator 클래스도 추상클래스이기 때문에 cost 추상메소드를 구현하지 않을 수 있었고, 추가로 getDescription 메소드를 추상메소드로 오버라이딩하였기 때문에 Whip과 Shot과 같은 추가 옵션 클래스에서 두 추상메소드를 모두 구현해주어야 한다.

그러면 이제 해당 데코레이터 패턴으로 디자인된 음료클래스를 고객클래스에서 어떻게 주문하는지 보자.

```java
public class Customer {
 
    public static void main(String[] args) {
        
        Beverage MyCoffee = new CaffeMocha();
        //추가시킬 옵션의 매개변수로 자신의 인스턴스를 전달
        MyCoffee = new Shot(MyCoffee);
        MyCoffee = new Shot(MyCoffee);
        MyCoffee = new Cream(MyCoffee);
        MyCoffee = new Whip(MyCoffee);
        MyCoffee = new JavaChip(MyCoffee);
        
        System.out.println("메뉴 : " + beverage.getDescription());
        System.out.println("가격 : " + beverage.cost());
        
    }
}
```

Customer 클래스에서 2샷, 크림, 휘핑크림, 자바칩까지 추가한 악마의 카페모카를 주문 한 상황을 표현해 보았다.
모든 옵션은 1번 추가에 500원이라고 설계를 했을 때, 실행 결과는
```
메뉴 : 카메모카, 샷, 샷, 크림, 휘핑크림, 자바칩
가격 : 7300
```
라고 나올 것이다.

코드에서 보았듯이 옵션을 추가할 때 자신의 인스턴스를 다시 전달함으로써, JavaChip - Whip - Cream - Shot - Shot - CaffeMocha로 감싸지는 형태로 객체가 생성되게 된다.
옵션들의 Beverage타입의 beverage 멤버를 계속 다음으로 전달하여 저런 형태의 체인이 만들어 지게 되는데,

이때 제일 외부의 JavaChip 객체의 getDescription 메소드를 실행시키면, beverage에 저장 되어 있는 Whip 객체의 getDescription으로, 그리고 이어서 Cream 객체에 있는 getDescription 메소드가 차례로 호출 되어 최종적으로 CaffeMocha 객체까지 도달하여 CaffeMocha 객체의 getDescription 메소드까지 호출하는 것이다.

그리고 리턴은 CaffeMocha 객체에 있는 description부터 차례로 리턴되면서 2개의 Shot 객체와 Cream 객체를 거치며 문자열이 계속 더해져 최종적으로는 "카메모카, 샷, 샷, 크림, 휘핑크림, 자바칩"라는 문자열이 완성된다.

cost 메소드 또한 마찬가지로 호출이 자바칩 -> ... -> 카메모카로 파고들어가서 카페모카에서 부터 차근차근 가격이 더해진다.

지금처럼 카페의 음료에 옵션을 추가하여 가격이 더해지고, getDescription에서 문자열이 더해지는 설명은 데코레이터 패턴을 이해하기 쉽게 표현한 것이고,
실질적으로는 추가적인 기능들을 덧붙이는 것으로 사용된다.
대표적으로 데코레이터 패턴이 쓰이는 곳은 우리가 가장 많이 사용하는 자바의 I/O 클래스이다. 

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
br.readLine();
```
를 풀어서 본다면 

```java
BufferedReader br = new InputStreamReader(System.in);
br = new BufferedReader(br);
br.readLine();
```

처럼 데코레이터 패턴이 적용됨을 알 수 있다.

---

## Adapter
#### 어댑터 패턴(Decorator Pattern)이란 한 클래스의 인터페이스를 클라이언트에서 사용하고자 할 때, 다른 인터페이스로 변환시켜 사용하는 패턴이다. 
#### 어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있다.

어댑터 패턴은 우리가 여행용 전원 어댑터를 생각해보면 이해가 쉽다.

우리가 사용하는 휴대폰, 노트북 충전기는 220V 동그란 돼지코 한국의 표준 플러그를 사용하지만, 전세계별로 이 플러그 표준이 각기 다 다르다.

일본은 동그란 모양이 아닌 || 모양을 표준으로 사용하고, 호주는 ∴ 모양을 표준으로 사용한다. 

그렇기 때문에 우리가 해당 나라에서 여행을 가서 콘센트를 사용해 충전을 하기 위해서는 아래 사진과 같은 전원 어댑터가 필요하다.

<p align="center"><img src="./img/adapter.jpg"></p>

이와 같이 어댑터는 소켓의 인터페이스를 플러그에서 필요로 하는 인터페이스로 바꿔준다고 할 수 있다.

객체 지향 프로그램에서의 어댑터도 마찬가지로 일상 생활에서와 동일하게 어떤 인터페이스를 클라이언트에서 요구하는 형태의 인터페이스로 맞춰주기 위해 중간에서 연결시켜주는 역할을 한다.

아래 어댑터의 기능을 잘 표현하는  UML이 있어서 가져와 보았다.
약간의 이해를 더 돕기 위해 MediaPackage라는 이름을 VideoPlayer으로,
Media Player는 AudioPlayer라는 이름으로 변경하여 구현하였다.

<p align="center"><img src="./img/adapter.png"></p>

아래는 AudioPlayer 인터페이스와 AudioPlayer 인터페이스를 구현하는 MP3 클래스이다.

> AudioPlayer.java
```java
public interface AudioPlayer{
   
   void play(String filename);
   
}
```

> MP3.java
```java
public class MP3 implements AudioPlayer{
   
   @Override
   void play(String filename){
      System.out.println("Playing MP3 File ♪ : "filename);
   }
   
}
```

아래는 VideoPlayer 인터페이스와 VideoPlayerr 인터페이스를 구현하는 MP4, MKV 클래스이다.

> VideoPlayer.java
```java
public interface VideoPlayer{
   
   void play(String filename);
   
}
```

> MP4.java
```java
public class MP3 implements VideoPlayer{
   
   @Override
   void play(String filename){
      System.out.println("Playing MP4 File ▶ : "filename);
   }
   
}
```

> MKV.java
```java
public class MKV implements VideoPlayer{
   
   @Override
   void play(String filename){
      System.out.println("Playing MKV File ▶ : "filename);
   }
   
}
```

아래는 VideoPlayer포맷을 AudioPlayer포맷에서도 사용할 수 있게 도와주는 FormatAdapter Class이다.
FormatAdapter Class는 AudioPlayer 인터페이스를 상속받고, 멤버 변수로 VideoPlayer를 사용한다.
생성자로 VideoPlayer를 입력받아 해당 Video 포맷을 사용하는 것이다.

> FormatAdapter.java
```java
public class FormatAdapter implements AudioPlayer{
   
   private VideoPlayer media;
   
   public FormatAdapter(VideoPlayer video){
      this.media = video;
   }
   
   @Override
   void play(String filename){
      System.out.println("Using Adapter : ");
      media.playFile(filename);
   }
   
}
```

아래 Main Class는 어댑터 패턴의 사용 예시이다.

MP3 인스턴스를 AudioPlayer 참조변수로 mp3Player 객체를 생성하였는데,

MP4 인스턴스에 어댑터를 사용하면 MP4도 mp3Player에서도 사용할 수 있게된다.

>Main.java
```java
public class Main{

   public static void main(String[] args){
   
   AudioPlayer mp3Player = new MP3();
   mp3Player.play("file.mp3");
   
   mp3Player = new FormatAdapter(new MP4());
   mp3Player.play("file.mp4");
   
   mp3Player = new FormatAdapter(new MKV());
   mp3Player.play("file.mkv");
   
   }
   
}
```

위 코드를 실행시켜보면 아래와 같이 출력이 됨을 알 수 있다.

```
> Playing MP3 File ♪ : file.mp3
> Using Adapter : Playing MP4 File ▶ : file.mp4
> Using Adapter : Playing MKV File ▶ : file.mkv
```

이렇게 어댑터 패턴을 통해 mp3Player 에서도 video 포맷의 파일을 재생시킬 수 있다. 물론 영상은 못보고 소리만 나오겠지만..

---
