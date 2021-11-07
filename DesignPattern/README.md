# Design Pattern

### Design Pattern이란? 
디자인 패턴은 주로 객체 지향 프로그래밍 설계 설계에서 공통적으로 발생하는 문제를 피하기 위해 자주 쓰이는 설계 방법을 패턴화한 것이다.
디자인 패턴은 의사소통 수단이 될 수 있고, 이를 참고하여 개발할 경우 개발의 효율성, 유지보수성, 운용성, 성능 최적화에 유리하다.

크게 생성, 구조, 행위 3가지 패턴으로 디자인 패턴을 구분지을 수 있다.

**1. 생성 패턴**
   - [Builder](#builder)
   - Prototype
   - [Factory Method](#factory-method)
   - [Abstract Factory](#abstract-factory)
   - [Singleton](#singleton)

**2. 구조 패턴**
   - [Bridge](#bridge)
   - [Decorator](#decorator)
   - [Facade](#facade)
   - Flyweight
   - Proxy
   - [Composite](#composite)
   - [Adapter](#adapter)

**3. 행위 패턴**

   - [Interpreter](#interpreter)
   - [Template Method](#template-method)
   - Chain of Responsibillity
   - [Command](#command)
   - [Iterator](#iterator)
   - Mediator
   - Memento
   - [Observer](#observer)
   - State
   - [Strategy](#strategy)
   - [Visitor](#visitor)

<hr>

# 1. 생성패턴 

<br>

# Builder

복잡한 객체에 대해 `생성(contruction)과 표현(representation)을 분리`함으로써 **똑같은 생성 과정으로 서로 다른 객체 표현**을 가능하게 하는 생성 디자인 패턴

<br>

## Builder 패턴을 사용해야하는 이유

1. `Immutability` - 객체의 불변성을 유지할 수 있음
2. `Named Parameter with Chaining` - 체이닝을 통한 명명된 매개변수 사용으로 가독성 증진
3. `Design Flexibility` - 필수적인 변수와 선택적인 변수를 각각 생성 가능
4. `Easy Maintenance` - 새로운 멤버가 추가되더라도 기존의 객체 생성 코드를 수정할 필요 없음
5. `Avoid RuntimeException` - 객체 생성 과정에서 유효성 검사를 통해 논리적인 에러를 막을 수 있음

> 💡 불변적인 객체로 구현해야하는 이유  
> - 불변성(`Immutability`)이란? 객체가 초기에 한번 생성된 이후에는 절대 상태를 바꾸지 않는 것을 말한다. 객체 생성시에 모든 정보가 주어지고 객체의 생애 주기 동안에는 상태가 바뀌지 않는 것이 특징이다.
> - 사용이 쉽다
> - Thread Safe 하다. 동기화할 필요가 없다.
> - 자유롭게 공유할 수 있다.

## Builder 패턴의 한계

코드를 2배정도 많이 사용하게 된다. 따라서 설정해야 할 매개변수가 적을 경우에는 일반 생성자를 통한 생성이 더욱 편할 수도 있다.

<br>

## 구현

### Builder 패턴 적용 전

일반적으로는 생성자(Constructor)를 통해 객체를 생성할 것이다. 생성자를 사용할 경우 멤버를 선택적으로 생성하기 어렵다.

#### 1. 생성자를 사용한 생성 - 자바빈즈 패턴(JavaBeans Pattern)

매개변수가 없는 기본 생성자를 통해 객체를 생성한 뒤, **Setter 메서드**를 통해 멤버를 설정하는 방식이다.

[클래스 정의]

```java
public User() {

}

public User(String firstName, String lastName, int age, String phone, String address) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.phone = phone;
    this.address = address;
}
```

[객체 생성]

```java
User user1 = new User("ssafy", "Kim", 6, "02-666-6666", "서울시 강남구 테헤란로 212 멀티캠퍼스");
User user2 = new User("ssafy", "Lee", 5, null, null);

User user3 = new User();
user3.setFirstName("ssafy");
user3.setLastName("Choi");
```

#### 2. 생성자를 사용한 생성 - 점층적 생성자 패턴(Telescoping Constructor Pattern)

필수 매개변수만을 가진 생성자를 만들고 선택 매개변수를 하나씩 추가한 생성자를 만든다. 수많은 **생성자 오버로딩**을 통해 원하는 형태의 객체를 생성하도록 하는 방식이다.

[객체 생성]

```java
public User (String firstName, String lastName, int age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.phone = null;
    this.address = null;
}

public User (String firstName, String lastName, int phone) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = null;
    this.phone = phone;
    this.address = null;
}
```

> 위 예제와 같이 생성자를 통한 객체 생성 시 필수 매개변수와 선택 매개변수를 구분하여 구현하기가 어렵다. 특히 매개변수가 많아진다면 일일히 setter를 부르는 일도, 매개변수 자리를 세주는 것도 일이다. 생성자를 경우의 수 별로 구현하는 것은 더욱 끔찍할 것이다.

### Builder 패턴 적용 후

[클래스 정의]

```java
// 클래스를 Final로 설정하여 확장이 불가능하며 불변성이 유지됨
public final class User 
{
    // 불변성을 유지하기 위해 private final로 설정
    private final String firstName;     // 필수 변수
    private final String lastName;      // 필수 변수
    private final int age;              // 선택 변수
    private final String phone;         // 선택 변수
    private final String address;       // 선택 변수

    private User(UserBuilder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.phone = builder.phone;
        this.address = builder.address;
    }
 
    // Setter를 구현하지 않음으로써 불변성 유지
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public int getAge() {
        return age;
    }
    public String getPhone() {
        return phone;
    }
    public String getAddress() {
        return address;
    }
 
    @Override
    public String toString() {
        return "User: "+this.firstName+", "+this.lastName+", "+this.age+", "+this.phone+", "+this.address;
    }
 
    // 객체 내부에 Builder 정의(중첩 클래스)
    public static class UserBuilder 
    {
        // 필수적인 변수만 final로 설정
        private final String firstName;     // 필수 변수
        private final String lastName;      // 필수 변수
        private int age;                    // 선택 변수
        private String phone;               // 선택 변수
        private String address;             // 선택 변수

        // Builder 생성자 매개변수는 필수 변수만을 포함
        public UserBuilder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        // 선택적인 변수는 추가적인 메서드를 구현하여 생성
        public UserBuilder age(int age) {
            this.age = age;
            return this;
        }
        public UserBuilder phone(String phone) {
            this.phone = phone;
            return this;
        }
        public UserBuilder address(String address) {
            this.address = address;
            return this;
        }
        // Builder로 생성된 객체 반환
        public User build() {
            User user =  new User(this);
            if (!validateUserName(user)) throw new NoNameException();
            if (!validateUserAge(user)) throw new InvalidAgeException();
            return user;
        }
        private boolean validateUserName(User user) {
            if (user.firstName==null || user.lastName==null) {
                if (user.age!=null || user.phone!=null || user.address!=null) return false;
            }
            return true;
        }
        private boolean validateUserAge(User user) {
            if (user.age<0) return false;
            return true;
        }
    }
}
```

[객체 생성]

```java
User user1 = new User.UserBuilder("ssafy", "Kim")
                     .age(6)
                     .phone("02-666-6666")
                     .address("서울시 강남구 테헤란로 212 멀티캠퍼스")
                     .build();

User user2 = new User.UserBuilder("ssafy", "Lee")
                     .age(5)
                     // no phone
                     // no address
                     .build();

User user3 = new User.UserBuilder("ssafy", "Choi")
                     // no age
                     // no phone
                     // no address
                     .build();
```

> 빌더 패턴을 이용해 위와 같이 하나의 생성자만으로 여러 상태의 객체를 생성할 수 있게되었다.

- 멤버를 `final`로 설정하여 불변성을 유지할 수 있다.
- 선택적인 변수의 경우 `null`로 설정할 필요가 없다. 또한 생성자 오버로딩을 하지 않아 선택적인 변수를 가진 객체도 동일한 방법으로 생성할 수 있다.
- 각 변수의 이름에 해당하는 메서드를 chaining 방식으로 접근하여 초기화할 수 있다. 따라서 **생성자의 매개변수 순서를 기억할 필요가 없고, 생성 과정에서의 가독성이 훨씬 좋아진다**.
- 만약 **새로운 멤버 변수가 추가되더라도 기존 객체 생성 코드를 수정하지 않아도 된다**. 새롭게 추가된 멤버 변수도 선택적인 매개변수와 동일하게 처리하기 때문이다.
- 추가적으로 **빌더 클래스 내부에 유효성 검사 메서드를 추가**한다면 멤버 생성 과정에서의 논리적인 에러를 사전에 차단할 수 있다.

<hr>

#### References

[howtodoinjava Builder Pattern](https://howtodoinjava.com/design-patterns/creational/builder-pattern-in-java/)  
[dzone Immutability and Builder Pattern](https://dzone.com/articles/immutability-with-builder-design-pattern)  
[StackExchange Why do we need a builder class](https://softwareengineering.stackexchange.com/questions/380397/why-do-we-need-a-builder-class-when-implementing-a-builder-pattern)  

<br>

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
> 
> Shoes 클래스는 추상 메소드가 존재하지 않지만 추상 클래스로 선언되었기 때문에 new Shoes();로 직접 객체 생성은 불가능하고,
> 
> Shoes 클래스를 상속받는 클래스에서 Shoes를 참조변수로하여 객체 생성을 할 수 있다.
> 
> 추상 메소드 존재 O -> 무조건 클래스도 추상 클래스로 선언
> 
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
```
위 코드를 실행해보면 아래와 같이 출력될 것이다.


```
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
 
    public BlackShoes(ShoesIngredientFactory shoesIngredientFactory) {
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
 
    public BrownShoes(ShoesIngredientFactory shoesIngredientFactory) {
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
 
    public RedShoes(ShoesIngredientFactory shoesIngredientFactory) {
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
---

# 2. 구조패턴

## Bridge

![image](https://user-images.githubusercontent.com/51703260/139539726-1d068a79-2be8-4053-98fe-13c9cdd3e0b2.png)

### Bridge 패턴이란?

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

### Bridge 패턴 구조

![image](https://user-images.githubusercontent.com/51703260/139540113-b331333f-b29c-46e8-94ea-308bcaad7ae8.png)

- `Client` : 일반적으로 클라이언트는 추상화 작업에만 관심이 있지만 추상화 개체를 구현 개체 중 하나와 연결하는 것은 클라이언트가 해주어야 하는 작업이다.

- `Abstraction` : 기능 계층의 최상위 클래스. 구현 부분에 해당하는 클래스를 인스턴스를 가지고 해당 인스턴스를 통해 구현 부분의 메서드를 호출한다.

- `Refined Abstraction` : 기능 계층에서 새로운 부분을 확장한 클래스

- `Implementation` : Abstraction의 기능을 구현하기 위한 인터페이스 정의

- `ConcreteImplementions` : 실제 기능을 구현한다.

### Bridge 패턴 구현

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

### Bridge 패턴의 활용
- 여러 플랫폼에서 사용해야 할 그래픽스 및 윈도우 처리 시스템에서 유용하게 쓰인다.
- 인터페이스와 실제 구현부를 서로 다른 방식으로 변경해야 하는 경우에 유용하게 쓰인다.

### Bridge 패턴의 장점
- 조합의 개수가 늘어남으로써 발생하는 기하급수적인 클래스 확장을 막을 수 있다.
- 구현을 인터페이스에 완전히 결합시키지 않았기 때문에 구현과 추상화된 부분을 분리시킬 수 있다.
- 추상화된 부분과 실제 구현 부분을 독립적으로 확장할 수 있습니다.
- 추상화된 부분을 구현한 구상 클래스를 바꿔도 클라이언트 쪽에는 영향을 끼치지 않는다.

### Bridge 패턴의 단점
- 응집도가 높은 클래스에 적용하면 코드와 디자인이 더 복잡해진다는 단점이 있다.

### Reference
[https://refactoring.guru/design-patterns/bridge](https://refactoring.guru/design-patterns/bridge)

---

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

이런것이 일종의 전자레인지 퍼사드라고 이해해도 좋을 것 같다.

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

### 사용용도

- 퍼사드 패턴은 퍼사드 클래스가 서브시스템 클래스들을 캡슐화를 해주는 기능을 제공하는 것 보다, 서브시스템 기능들을 편리하게 사용할 수 있는 인터페이스를 제공하는 것이 주된 목적이다.
- 클라이언트와 구현 클래스 또는 서브시스템과 다른 서브시스템간에 의존관계가 많을 경우 이를 감소시켜 각 서브시스템들의 독립성과 이식성을 높이는것을 목적으로 사용한다.

### 장단점

#### 장점
- 클라이언트가 다뤄야 할 객체의 수를 줄여준다.
- 클라이언트와 서브시스템 간의 결합도가 높아 복잡할 때 퍼사드 패턴을 활용하면 간편해진다.

#### 단점
- 클라이언트에게 내부 서브시스템까지 숨길 수는 없다.
- 클라이언트가 서브시스템 내부의 클래스를 직접 사용하는 것을 막을 수 없다.

---

## Composite

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

지금까지의 `Product` `Box`를 예시로 Composite Pattern을 간단하게 구현해보자.

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

### Reference
[https://refactoring.guru/design-patterns/composite](https://refactoring.guru/design-patterns/composite)

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

# 3.행위 패턴
# Interpreter

## 동기
문제를 해결하기 위해 간단한 언어(미니 언어)를 만들어야 하는 경우가 있는데, 조금만 확장이 일어나도 코드가 복잡해져 불편했던 경우가 있다. 이러한 문제를 해결하기 위해 등장한 패턴이 인터프리터 패턴이다.

## 설명

문제를 해결하기 위한 간단한 미니 언어를 해석해서 실행하기 위한 통역 프로그램이다. (언어 분석기라고 생각하면 된다.)

언어 문법이나 표현을 평가하는 방법을 제공하는 행동 패턴(Behavioral pattern) 중 하나이다. 

이 패턴은 SQL 구문 분석, 기호 처리 엔진 등에 사용된다.

## BNF

BNF는 컴퓨터 언어에서 언어의 문법을 수학적인 수식으로 나타낼 때 사용하는 언어 도구이다.

대부분의 언어 구조가 BNF 형태로 표현할 수 있기 때문에 언어를 해석할 때 BNF 형태로 나타내는 경우가 많다.

인터프리터 패턴도 BNF 구조의 언어를 해석할 수 있도록 디자인 되었습니다.

> ## (( not X ) and ( Y or Z )) 의 경우
BNF로 표현된 Syntax의 언어를 분석해서 그림으로 나타내면 아래와 같다.
![image](https://user-images.githubusercontent.com/53392870/139570650-5d23af40-0d40-4283-995e-e49fdcc8f903.png)
[출처](https://palpit.tistory.com/entry/Design-Pattern-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0Interpreter-%ED%8C%A8%ED%84%B4-%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4)

위의 그림을 보면 BNF에서 Terminal Expression과 Non-Terminal Expression이 존재합니다.
인터프리터 패턴 역시 Expression 인터페이스와 Terminal Expression, Non-Terminal Expression을 나타내는 클래스로 구성되어 있습니다.

### 장점
- 문법의 추가 및 수정, 구현이 쉬워진다.
각 문법 규칙을 클래스로 표현하기 때문에 언어를 쉽게 구현할 수 있다.
문법이 클래스에 의해 표현되기 때문에 언어를 쉽게 변경하거나 확장할 수 있다.
Expression 인터페이스에 메소드만 추가하면 프로그램을 해석하는 기본 기능 외에 보기 쉽게 출력하는 기능이나 더 나은 프로그램 확인 기능과 같은 새로운 기능을 추가할 수 있다.

### 단점
- 복잡한 문법의 경우 관리 및 유지가 어려워진다.
문법이 복잡해질 경우 인터프리터 패턴으로 구현하기 보다는 파서 생성기와 같은 도구를 이용하는 것이 낫다.
- 효율성이 별로 좋지 않다. 따라서 효율성이 고려 사항이 아닌 경우 사용하는 것이 좋다.
 
## 구성

1. Context Class
문장을 저장하고 관리하는 클래스이다.
2. Expression Interface
문장 해석을 위한 인터페이스, interpret 메소드는 문장 해석을 위한 메소드이고 하위 클래스에서 기능을 구현한다.
3. Terminal Expression Class
문장 해석의 끝을 의미한다.
4. Non Terminal Expression Class
문장 해석에서 계속해서 전개되는 표현식이다.

## 예제
![image](https://user-images.githubusercontent.com/53392870/139569414-1c633f8a-41d5-4bd1-969c-c73cc5b607cb.png)

1. Expression 인터페이스: 문장을 해석하기 위한 인터페이스, interpret()을 정의한다.
```java
public interface Expression {
	public boolean interpret(String context);
}
```
2. TerminalExpression 클래스: 인터페이스를 구현하는 클래스로 context의 인터프리터 역할을 한다.
```java
public class TerminalExpression implements Expression {
	
	private String data;
	
	public TerminalExpression(String data) {
		this.data = data;
	}
	
	@Override
	public boolean interpret(String context) {
		if (context.contains(data)) return true;
		else return false;
	}
}
```
3. AndExpression, OrExpression 클래스: Expression을 구현하고 조합식을 만드는 데 사용한다.
```java
public class AndExpression implements Expression{
	
	private Expression expr1;
	private Expression expr2;
	
	public AndExpression(Expression expr1, Expression expr2) {
		this.expr1 = expr1;
		this.expr2 = expr2;
	}
	
	@Override
	public boolean interpret(String context) {
		return expr1.interpret(context) && expr2.interpret(context);
	}
}
```
```java

public class OrExpression implements Expression{
	
	private Expression expr1;
	private Expression expr2;
	
	public OrExpression(Expression expr1, Expression expr2) {
		this.expr1 = expr1;
		this.expr2 = expr2;
	}
	
	@Override
	public boolean interpret(String context) {
		return expr1.interpret(context) || expr2.interpret(context);
	}
}
```
4. Main 클래스: Expression 클래스를 사용하여 규칙을 만들고 식을 구문 분석한다.
```java

public class Main {
	// Rule: Robert and John are male
	public static Expression getMaleExpression() {
		Expression robert = new TerminalExpression("Robert");
		Expression john = new TerminalExpression("John");
		return new OrExpression(robert, john);
	}

	// Rule: Julie is a married women
	public static Expression getMarriedWomanExpression() {
		Expression julie = new TerminalExpression("Julie");
		Expression married = new TerminalExpression("Married");
		return new AndExpression(julie, married);
	}

	public static void main(String[] args) {
		Expression isMale = getMaleExpression();
		Expression isMarriedWoman = getMarriedWomanExpression();

		System.out.println("John is male? " + isMale.interpret("John"));
		System.out.println("Julie is a married women? " + isMarriedWoman.interpret("Married Julie"));
	}
}
```

<hr>

# Template Method

템플릿 메소드 패턴은 알고리즘의 구조를 메소드에 정의하고, 하위 클래스에서 알고리즘 구조의 변경없이 알고리즘을 재정의하는 디자인 패턴입니다.

알고리즘이 단계별로 나누어지거나 같은 역할을 하는 메소드이지만 여러 곳에서 다른 형태로 사용이 필요한 경우 유용하게 사용됩니다.

변하지 않는 기능을 상위 클래스에 만들어두고 상속해 사용하고 자주 변경하며 확장할 기능은 하위 클래스에서 만들도록 하는 방식입니다.

구현 방식은 추상클래스와 구현클래스로 작성하며, 메인이 되는 로직 부분은 추상 클래스의 일반 메소드로 선언해서 사용합니다.
그 후 구현마다 달라질 수 있는 메소드들은 구현 클래스에서 선언 후 호출하는 방식으로 사용됩니다.

<img width="389" alt="template-method-pattern" src="https://user-images.githubusercontent.com/16794320/138582598-26b8076a-7258-4cd4-b33d-ec6146750633.png">

```java

//AbstractClass.java
public abstract class AbstractClass {
    
    protected abstract void hook1();
    
    protected abstract void hook2();
    
    public void templateMethod() {
        hook1();
        hook2();
    }
    
}
//ConcreteClass.java
public class ConcreteClass extends AbstractClass {

    @Override
    protected void hook1() {
        System.out.println("ABSTRACT hook1 implementation");
    }

    @Override
    protected void hook2() {
        System.out.println("ABSTRACT hook2 implementation");
    }

}
//TemplateMethodPatternClient.java
public class TemplateMethodPatternClient {
    public static void main(String[] args) {
        AbstractClass abstractClass = new ConcreteClass();
        abstractClass.templateMethod();
    }
}

```
## 장점
1. 코드의 중복 제거
2. 자식 클래스의 역할을 줄여서 핵심 로직의 관리가 용이
3. 코드를 객체지향적으로 구성할 수 있다.
## 단점
1. 추상 메소드가 많아져 클래스 관리가 복잡하다.
2. 클래스간의 관계와 코드가 꼬일 수 있다.

예제
AbstractMap<K,V>
```java
public V get(Object key) {
    Iterator<Entry<K,V>> i = entrySet().iterator();
    if (key==null) {
        while (i.hasNext()) {
            Entry<K,V> e = i.next();
            if (e.getKey()==null)
                return e.getValue();
        }
    } else {
        while (i.hasNext()) {
            Entry<K,V> e = i.next();
            if (key.equals(e.getKey()))
                return e.getValue();
        }
    }
    return null;
}

```
HashMap<K,V> extends AbstractMap<K,V> 의 get()
```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

```
public class TreeMap<K,V> extends AbstractMap<K,V> 의 get() 메소드
```java
public V get(Object key) {
    Entry<K,V> p = getEntry(key);
    return (p==null ? null : p.value);
}
```

## 추상클래스 vs 인터페이스 (Java 8 기준)
* 추상클래스와 인터페이스는 인스턴스화 하는 것은 불가능하며, 구현부가 있는 메소드와 없는 메소드 모두 가질 수 있다는 점에서 유사하다.
* 인터페이스에서 모든 변수는 기본적으로 public static final 이며, 모든 메소드는 public abstract 인 반면
추상클래스에서는 static 이나 final 이 아닌 필드를 지정할 수 있고, public, protected, private 메소드를 가질 수 있다.
* 인터페이스를 구현하는 어떤 클래스는, 다른 여러개의 인터페이스들을 함께 구현할 수 있다. 추상클래스는 상속을 통해 구현되는데, 자바에서는 다중상속을 지원하지 않으므로 추상클래스를 상속받은 서브클래스는 다른 클래스를 상속받을 수 없다.


## 추상클래스, 인터페이스 사용 예시
### 추상클래스
* 관련성이 높은 클래스 간에 코드를 공유하고 싶은 경우
* 추상클래스를 상속받은 클래스들이 공통으로 가지는 메소드와 필드가 많거나, public 이외의 접근제어자(protected, private) 사용이 필요한 경우
* non-static, non-final 필드 선언이 필요한 경우. 즉, 각 인스턴스에서 state 변경을 위한 메소드를 선언할 수 있다.
### 인터페이스
* 서로 관련성이 없는 클래스들이 인터페이스를 구현하게 되는 경우에 사용한다. 예를 들어, Comparable, Cloneable 인터페이스는 여러 클래스들에서 구현되는데, 구현클래스들 간에 관련성이 없는 경우가 대부분이다.
* 특정 데이터 타입의 행동을 명시하고 싶은데, 어디서 그 행동이 구현되는지는 신경쓰지 않는 경우.
* 다중상속을 허용하고 싶은 경우

# Command

커맨드(Command) 패턴은 특정 객체에 대한 특정 **작업 요청을 객체로 캡슐화**함으로써 주어진 여러 기능을 사용할 수 있는 재사용성이 높은 클래스를 설계하는 패턴입니다. 

이벤트가 발생했을 때 실행될 기능이 다양하면서도 변경이 필요한 경우에 이벤트를 발생 시키는 클래스를 변경하지 않고 재사용하고자 할 때 유용합니다.

Client가 보낸 요청을 객체로 만들어서 객체를 큐로 관리하고 저장,로깅,취소할 수도 있습니다.



<p align="center"><img src="img/command_example1.png" width="600"></p>

홈 오토메이션 리모컨을 만든다고 생각해봅시다.

1번 버튼에 Light가 연결되어 있으면 ligth.on(), GarageDoor가 연결되어 있으면 garageDoor.up()... 

각 버튼에 기능을 직접 연결한다면 기능들이 추가될 때마다 리모컨의 코드를 고쳐야합니다.

하지만, 커맨드 패턴을 적용한다면 버튼마다 커맨드 객체를 저장해 두어 사용자가 버튼을 눌렀을 때 커맨드 객체를 통해서 작업을 처리하도록 만들 수 있습니다.

그러므로 리모컨에서는 자세한 내용을 전혀 몰라도 됩니다. 

리모컨은 어떤 객체에 어떤 일을 시켜야 할지 잘 알고 있는 커맨드 객체만 있으면 됩니다.



## 커맨드 패턴의 구조와 구성요소

<p align="center"><img src="img/command_class_diagram.PNG" width="600"></p>

- Clinet

  - ConcreteCommand를 생성하고 Receiver를 설정합니다.
  - Invoker 객체의 setCommand() 메소드를 호출하여 커맨드 객체를 넘겨줍니다.

- Invoker

  - setCommand() 메소드를 통해 커맨드 객체를 저장하고 있습니다.
  - 저장된 커맨드 객체의 execute() 메소드를 호출함으로써 커맨드 객체에게 특정 작업을 수행해 달라는 요구를 하게 됩니다.

- Command

  - 모든 커맨드 객체에서 구현해야 하는 인터페이스입니다.
  - 행동과 receiver에 대한 정보가 들어있습니다.
  - 모든 명령은 execute() 메소드 호출을 통해 수행되며, 이 메소드에서는 receiver에 특정 작업을 처리하라는 지시를 전달합니다.

- Receiver

  - 기능을 수행합니다.
  - 요구 사항을 수행하기 위해 어떤 일을 처리해야 하는지 알고있는 객체입니다.

- ConcreteCommand

  - 특정 행동과 receiver 사이를 연결해 줍니다.
  - Invoker에서 execute() 호출을 통해 요청을 하면 ConcreteCommand 객체에서 receiver에 있는 메소드를 호출함으로써 그 작업을 처리합니다.
  - execute() 메소드 에서는 receiver에 있는 메소드를 호출하여 요청된 작업을 수행합니다.

  

## 커맨드 패턴의 동작 순서

1. Client에서 커맨드 객체를 생성

2. Invoker 객체의 setCommand() 메소드를 호출하여 커맨드 객체를 저장

3. Clinet에서 Invoker를 통해  execute() 요청을 전송

4. Invoker에서 커맨드 객체의 execute() 실행

5. 커맨드 객체의 Receiver가 수행.

   

- Light

  ```java
  //Receiver 역할
  public class Light { 
  	
  	private String location;
  	
      public Light(String location) {
      	this.location = location;
      }
      
      public void on(){        
          System.out.println(location + " Light is on");
      }
  
      public void off(){        
          System.out.println(location + " Light is off");
      }               
  
  }
  ```

  

- Command

  ```java
  public interface Command {
  	void execute();
  }
  ```



- LightOnCommand

  ```java
  public class LightOnCommand implements Command {
  	
  	Light light;
  	
  	public LightOnCommand(Light light) {
  		super();
  		this.light = light;
  	}
  
  	@Override
  	public void execute() {
  		light.on();
  	}
  
  }
  
  ```

  

- LightOffCommand

  ```java
  public class LightOffCommand implements Command {
  	
  	Light light;
  	
  	public LightOffCommand(Light light) {
  		super();
  		this.light = light;
  	}
  
  	@Override
  	public void execute() {
  		light.off();
  	}
  
  }
  ```

  

- StereoOnWithCDCommand

  ```java
  public class StereoOnWithCDCOmmand implements Command {
  	
  	Stereo stereo;
  	
      public StereoOnWithCDCOmmand(Stereo stereo){
          this.stereo = stereo;
      }
      
      // execute에서 여러 개의 동작을 수행하는 로직을 작성할 수 있습니다.
      public void execute() {
          stereo.On();
          stereo.SetCD();
          stereo.SetVolume(11);
      }
  }
  ```

  

- RemoteController

  ```java
  // Invoker 역할
  public class RemoteController {
  	
  	static final int SIZE = 7;
  	Command[] onCommands;
      Command[] offCommands;
  
      public RemoteController() {
  
          onCommands = new Command[SIZE];
          offCommands = new Command[SIZE];
          
          // null 처리를 대신할 커맨드
          Command noCommand = new NoCommand();
  
          for (int i = 0; i < SIZE; i++) {
              onCommands[i] = noCommand;
              offCommands[i] = noCommand;
          }
      }
  
      public void setCommand(int slot, Command onCommand, Command offCommand) {
          onCommands[slot] = onCommand;
          offCommands[slot] = offCommand;
      }
  
      public void onButtonWasPushed(int slot) {
          onCommands[slot].Execute();
      }
  
      public void offButtonWasPushed(int slot) {
          offCommands[slot].Execute();
      }
      
      @Override
      public String toString(){
          StringBuffer sb = new StringBuffer();
          sb.append("\n------ Remote Control -----\n");
  
          for (int i = 0; i < onCommands.length; i++) {
              sb.append("[slot " + i + "] " + 
                  onCommands[i].getClass().getName() + "    " + 
                  offCommands[i].getClass().getName() + "\n");
          }
         return sb.toString();
      }
  }
  ```



- Client

  ```java
  //Client 역할
  public class RemoteControlTest {
  
  	public static void main(String[] args) {
  		RemoteController remoteController = new RemoteController();
  		
  		Light livingRoomlight = new Light("Living Room");
  		Light kitchenlight = new Light("Kitchen");
  		
  		LightOnCommand livingRoomlightOn =
  				new LightOnCommand(livingRoomlight);
  		LightOffCommand livingRoomlightOff =
  				new LightOffCommand(livingRoomlight);
  		LightOnCommand KitchenlightOn =
  				new LightOnCommand(kitchenlight);
  		LightOffCommand KitchenlightOff =
  				new LightOffCommand(kitchenlight);
  		
  		remoteController.setCommand(1, livingRoomlightOn, livingRoomlightOff);
  		remoteController.setCommand(2, KitchenlightOn, KitchenlightOff);
  		
  		remoteController.onButtonWasPushed(1);
  		remoteController.offButtonWasPushed(1);
  		
          System.out.println(remoteController);
          
  		remoteController.onButtonWasPushed(2);
  		remoteController.offButtonWasPushed(2);
  	}
  
  }
  ```

  <p align="center"><img src="img/command_example2.PNG" width="600"></p>



## 커맨드 패턴의 장단점

- 장점
  - 작업을 요청하는 객체와 수행하는 객체를 분리하여 의존성을 줄이고 단일 책임 원칙(SRP)을 만족합니다
  - 코드의 수정 없이 작업 수행 객체나 추가 구현이 가능하여 개방-폐쇄 원칙(OCP)을 만족합니다.

- 단점
  - 커맨드가 추가되면 클래스를 계속 생성해야 합니다.



## 커맨드 패턴 활용

- 작업 큐

  - 커맨드 객체를 생성하고 큐에 추가합니다. ex) 네트워크 연결, 다운로드 ...
  - 스레드에서 큐로부터 커맨드를 하나씩 제거하면서 커맨드의 execute() 메소드를 호출합니다.

  

- 요청을 로그에 기록

  - 커맨드에 save()와 load() 메소드를 추가합니다.

  - 각 커맨드가 실행될 때 마다 디스크에 그 내역을 저장합니다.

  - 시스템이 다운되었다가 복구할 때 그 저장 기록으로 커맨드 객체를 다시 실행할 수 있습니다.

    

### Reference

http://latedreamer.blogspot.com/2017/02/command-pattern.html

https://brownbears.tistory.com/561

<br>

---

# Iterator

컬렉션 구현 방법을 노출시키지 않으면서 그 집합체 안에 들어있는 모든 항목에 접근할 수 있는 방법을 제공한다.

컬렉션 객체 안에 들어있는 모든 항목에 접근하는 방식이 통일되어 있으면 어떤 종류의 집합체에 대해서도 사용할 수 있는 다형적인 코드를 만들 수 있다.

이터레이터 패턴을 사용하면 모든 항목에 일일이 접근하는 작업을 컬렉션 객체가 아닌 반복자 객체에서 맡게 된다.

이렇게 하면 집합체의 인터페이스 및 구현이 간단해질 뿐 아니라, 집합체에서는 반복작업에서 손을 떼고 원래 자신이 할 일 (객체 컬렉션 관리) 에만 전념할 수 있다.

## 예제

두 개의 서로다른 식당이 있고 각각의 식당에서 메뉴를 구현한다고 가정해보자

```java
public class MenuItem {
	String name;
	String description;
	String vegetarian;
	double price;

	public MenuItem(String name, String description, boolean vegetarian, double price){
		this.nae = name;
		this.description = description;
		this.vegetarian = vegetarian;	
		this.price = price;
	}

	public String getName() {
		return name;
	}
	
	public String getDescription() {
		return description;
	}

	public double getPrice() {
		return price;
	}

	public boolean isVegetarian() {
		return vegetarian;
	}
	
}
```

```java
public class PancakeHouseMenu {
	ArrayList<MenuItem> menuItems;
	
	public PancakeHouseMenu() {
		this.menuItems = new ArrayList();
		additem("K&B 팬케이크 세트","스크램블드 에그와 토스트가 곁들여진 펜케이크",true,2.99);
		additem("레귤러 팬케이크 세트","달걀 후라이와 소시지가 곁들여진 펜케이크",false,2.99);
		additem("블루베리 펜케이크","신선한 블루베리와 블루베리 시럽으로 만든 펜케이크",true,3.49);
		additem("와플","와플, 취향에 따라 블루베리나 딸기를 얹을 수 있습니다.",true,3.59);
	}
	
	public void additem(string name, String description, boolean vegetarian, double price) {
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
		menuItem.add(menuItem);
	}
	
	public ArrayList<MenuItem> getMenuItems() {
		return menuItems;	
	}

	//기타 메소드
}
```

```java
public class DinerMenu {
	static final int MAX_ITEMS = 6;
	int numberOfItems = 0;
	MenuItem[] menuItems;
	
	public DinerMenu() {
		this.menuItems = new MenuItem[MAX_ITEMS];
		additem("채식주의자용 BLT","통밀 위에 (식물성)베이컨, 상추, 토마토를 얹은 메뉴",true,2.99);
		additem("BLT","통밀 위에 베이컨, 상추, 토마토를 얹은 메뉴",false,2.99);
		additem("오늘의 스프","감자 샐러드를 곁들인 오늘의 스프",false,3.29);
		additem("핫도그","사워크라우트, 갖은 양념, 양파, 치즈가 곁들여진 핫도그",false,3.05);
	}

	public void additem(string name, String description, boolean vegetarian, double price) {	
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
		if(nemberOfItems >= MAX_ITEMS){
			System.err.println("죄송합니다, 메뉴가 꽉 찼습니다. 더 이상 추가할 수 없습니다.");
		} else {
			menuItems[numberOfItems] = menuItem;
			numberOfItems = numberOfItems+1;
		}
	}
	
	public MenuItem[] getMenuItems() {
		return menuItems;
	}

	//기타 메소드

}
```

이 위의 두 개의 메뉴를 사용하는 클라이언트를 만들어보자.

클라이언트의 기능은 아래와 같다.

1. `printMenu()` : 메뉴에 있는 모든 항목 출력
2. `printBreakfastMenu()` : 아침 식사 항목만 출력
3. `printLunchMenu()` : 점심 식사 항목만 출력
4. `printVegetarianMenu()` : 채식주의자용 메뉴 항목만 출력
5. `isItemVegetarian(name)` : name 항목이 채식주의자용 메뉴이면 true, 아니면 false

```java
PancakeHouseMenu pancakeHouseMenu = new PancakeHouseMenu();
ArrayList<MenuItem> breakfastItems = pancakeHouseMenu.getMenuItems();

DinerMenu dinerMenu = new DinerMenu();
MenuItem[] lunchItems = dinerMenu.getMenuItems();

for ( int i=0; i < breakfaseItems.size(); i++) }
	MenuItem menuItem = breakfastItems.get(i);
	System.out.println(menuItem.getName());
	System.out.println(menuItem.getPrice());
	System.out.println(menuItem.getDescription());
}

for ( int i=0; i < lunchItems.length; i++) {
	MenuItem menuItem = lunchItems[i];
	System.out.println(menuItem.getName());
	System.out.println(menuItem.getPrice());
	System.out.println(menuItem.getDescription());
}
```

메뉴의 모든 항목을 출력하려면 위와 같은 코드를 작성하게 될 것이다.

다른 메소드들도 결국 위의 코드와 비슷하게 작성될 것이다.

항상 두 메뉴를 이용하고, 각 아이템에 대해서 반복적인 작업을 수행하기 위해 두 개의 순환문을 써야 한다.

이후에 메뉴가 더 추가된다면? 이 상황이 계속 반복될 것이다.

그렇면 반복을 분리해 `Iterator` 라는 객체를 만들자

```java
Iterator<MenuItem> iterator = breakfastMenu.createIterator();
while(iterator.hasNext()){
	MenuItem menuItem = iterator.next();
}

Iterator<MenuItem> iterator = lunchMenu.createIterator();
while(iterator.hasNext()){
	MenuItem menuItem = iterator.next();
}
```

사용자 정의 `Iterator` 인터페이스를 만들어도 되지만, `java.util.Iterator` 인터페이스를 사용해서 적용해보자

```java
public interface Menu {
	public Iterator<MenuItem> createIterator();
}
```

```java
public class PancakeHouseMenu **implements Menu** {
	ArrayList<MenuItem> menuItems;
	
	public PancakeHouseMenu() {
		this.menuItems = new ArrayList();
		additem("K&B 팬케이크 세트","스크램블드 에그와 토스트가 곁들여진 펜케이크",true,2.99);
		additem("레귤러 팬케이크 세트","달걀 후라이와 소시지가 곁들여진 펜케이크",false,2.99);
		additem("블루베리 펜케이크","신선한 블루베리와 블루베리 시럽으로 만든 펜케이크",true,3.49);
		additem("와플","와플, 취향에 따라 블루베리나 딸기를 얹을 수 있습니다.",true,3.59);
	}
	
	public void additem(string name, String description, boolean vegetarian, double price) {
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
		menuItem.add(menuItem);
	}
	
	public ArrayList<MenuItem> getMenuItems() {
		return menuItems;	
	}

	**@Override
	public Iterator<MenuItem> createIterator() {
		// ArrayList 컬렉션에 반복자를 리턴하는 iterator() 메소드 활용
		return menuItems.iterator();
	}**
}
```

```java
public class DinerMenuIterator implements Iterator<MenuItem> {
	Menuitem[] list;
	int position = 0;

	public DinerMenuIterator(MenuItem[] list) {
		this.list = list;
	}

	@Override	
	public MenuItem next() {
		MenuItem menuItem = list[position];
		position += 1;
		return menuItem;
	}

	@Override
	public boolean hasNext() {
		if(position >= list.length || list[position] == null) return false;
		else return true;
	}
	
	// 반드시 기능을 제공하지 않아도됨 그렇다면 java.lang.UnsupportedOperationException을 던지도록 하면됨
	@Override
	public void remove() { 
		if(position <= 0) 
			Throw new IllegalStateException("next()가 한번도 호출되지 않음.");
		
		if(list[position-1] != null){
			for(int i=position-1; i<(list.length-1); i++){
				list[i] = list[i+1];
			}
			list[list.length-1] = null;
		}
	}

}
```

```java
public class DinerMenu **implements Menu** {
	static final int MAX_ITEMS = 6;
	int numberOfItems = 0;
	MenuItem[] menuItems;
	
	public DinerMenu() {
		this.menuItems = new MenuItem[MAX_ITEMS];
		additem("채식주의자용 BLT","통밀 위에 (식물성)베이컨, 상추, 토마토를 얹은 메뉴",true,2.99);
		additem("BLT","통밀 위에 베이컨, 상추, 토마토를 얹은 메뉴",false,2.99);
		additem("오늘의 스프","감자 샐러드를 곁들인 오늘의 스프",false,3.29);
		additem("핫도그","사워크라우트, 갖은 양념, 양파, 치즈가 곁들여진 핫도그",false,3.05);
	}

	public void additem(string name, String description, boolean vegetarian, double price) {	
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
		if(nemberOfItems >= MAX_ITEMS){
			System.err.println("죄송합니다, 메뉴가 꽉 찼습니다. 더 이상 추가할 수 없습니다.");
		} else {
			menuItems[numberOfItems] = menuItem;
			numberOfItems = numberOfItems+1;
		}
	}
	
	public MenuItem[] getMenuItems() {
		return menuItems;
	}

	**@Override
	public Iterator<MenuItem> createIterator() {
		return new DinerMenuIterator(menuItems);
	}**

}
```

```java
public class Waitress {
	ArrayList<Menu> menus;
	
	public Waitress(ArrayList<Menu> menus) {
		this.menus = menus;
	}
	
	public void printMenu() {
		Iterator menuIterator = menus.iterator();

		while(menuIterator.hasNext()){
			Menu menu = menuIterator.next();			
			printMenu(menu.createIterator());
		}
	}
	
	private void printMenu(Iterator<MenuItem> iterator) {
		while(iterator.hasNext()) {
			MenuItem menuItem = iterator.next();
			System.out.println(menuItem.getName());
			System.out.println(menuItem.getPrice());
			System.out.println(menuItem.getDescription());		
		}
	}
}
```

```java
public class MenuTestDrive {
	public static void main(String args[]) {
		ArrayList<Menu> menuList = new ArrayList();
		menuList.add(new PancakeHouseMenu());
		menuList.add(new DinerMenu());
		
		Waitress waitress = new Waitress(menuList);
		waitress.printMenu();
	}
}
```

위와 같이 `Iterator`  로 분리한 후에  `Waitress` 클래스에서 컬렉션을 받아서 프린트하는 메소드를 작성하면 `main` 에서는 내부 로직을 신경쓰지 않고 모든 항목들에 대해 반복 작업을 수행할 수 있게 된다.

---

<br>

# Observer
## Observer 패턴이란?

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/137611370-063812ec-6f27-4e23-ab1b-975675c7bf9f.png" width="50%">
</p>

객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 **객체가 직접 목록의 각 옵저버에게 통지**하도록 하는 디자인 패턴이다. 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다. **발행/구독 모델**로 알려져 있기도 하다. <br>

즉, 한 객체의 상태 변화에 따라 다른 객체의 상태도 연동되도록 **일대다(one-to-many) 객체 의존 관계**를 구성하는 패턴 <br>

데이터의 변경이 발생했을 경우 상대 클래스나 객체에 의존하지 않으면서 데이터 변경을 통보하고자 할 때 유용하다.

<br>

## Observer 패턴의 구현
Observer 패턴을 구현하는 방법에는 여러가지가 있지만, 대부분 **주제(Subject) 인터페이스**와 **옵저버(Observer) 인터페이스**가 들어있는 클래스로 디자인합니다.

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/137612300-351dae9e-c816-43ff-b81d-89c79f71427a.jpeg" width="70%">
</p>

### Subject 인터페이스와 구현체
```java
interface Subject {
  registerObserver() // 옵저버 등록
  removeObserver() // 옵저버 삭제
  notifyObserver() // 옵저버에게 업데이트 알림
}

class SubjectImpl implements Subject {
  registerObserver() { ... }
  removeObserver() { ... }
  notifyObserver() { ... }
  
  getState() // 주제 객체는 상태를 설정하고 알기 위한 getter, setter가 있을 수 있다.
  setState()
}
```

### Observer 인터페이스와 구현체
```java
interface Observer{ // 옵저버가 될 객체에서는 반드시 Observer 인터페이스를 구현해야함.
  update() // 주제의 상태가 바뀌었을때 호출됨
}

class ObserverImpl implements Observer {
  update() { 
    // 주제가 업데이트 될 때 해야하는 일
  }
}
```

* Observer에서 `update()` 메소드를 통해 주제의 state를 전달받긴 하지만, 실제 state 데이터의 주인은 주제(Subject)에 있다.
* Observer는 데이터가 변경되었을 때 주제에서 데이터를 전달해주기를 기다리는 입장이기 때문에 의존성을 가진다고 할 수 있다.
* 이런 방법을 사용하면 여러 객체에서 동일한 데이터를 제어하도록 하는 것에 비해 더 깔끔한 객체지향 디자인을 만들 수 있다.

<br>

## 느슨한 결합(Loose Coupling)의 위력 (= Observer 패턴의 장점)
두 객체가 느슨하게 결합되어 있다는 것은, 그 둘이 상호작용을 하긴 하지만 서로에 대해 잘 모르는 것을 의미한다. Observer 패턴에서는 **주제와 옵저버가 느슨하게 결합되어 있는 객체 디자인을 제공**한다.

주제가 옵저버에 대해서 아는 것은 옵저버가 특정 인터페이스(Observer 인터페이스)를 구현한다는 것 뿐이기 때문이다.

<br>

느슨한 결합의 장점은 다음과 같다.

1. 옵저버를 언제든 새로 추가, 제거할 수 있다.
2. 새로운 형식의 옵저버를 추가하려고 할 때 주제를 전혀 변경할 필요가 없다.
3. 주제와 옵저버는 서로 독립적으로 재사용할 수 있다.
4. 주제나 옵저버가 바뀌더라도 서로에게 영향을 미치지 않는다.

<br>

* 디자인 원칙
> 서로 상호작용을 하는 객체 사이에서는 가능하면 느슨하게 결합하는 디자인을 사용해야 한다.

<br>

따라서 Loose Coupling 디자인을 활용하면 객체 사이의 상호 의존성을 최소화 할 수 있기 때문에 **변경사항이 생겨도 무난히 처리할 수 있는 유연한 객체지향 시스템을 구축**할 수 있다.

<br>

## Observer 패턴의 예시
### 기상 모니터링 애플리케이션 개요
이 시스템은 아래 세 요소로 이루어진다.

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/137611697-aaac0d8d-52ee-4c58-8ac2-848779a8e18e.jpeg" width="80%">
</p>

1. 기상 스테이션(실제 기상 정보를 수집하는 장비)
2. WeatherData 객체(기상 스테이션으로부터 오는 데이터를 추적하는 객체)
3. 사용자에게 현재 기상 조건을 보여주는 디스플레이

<br>

WeatherData 객체에서는 기상 스테이션 장비 자체로부터 데이터를 가져올 수 있다. 데이터를 가져온 후에는 디스플레이 장비에 세가지 항목을 표시할 수 있다.<br>
* 현재 조건(온도, 습도, 압력)
* 기상 통계
* 간단한 기상 예보

이 화면들은 모두 WeatherData 객체에서 최신 데이터로 업데이트될 때마다 실시간으로 갱신된다. 

<br>

### Bad Case (Observer 패턴 적용 전)

``` java
class WeatherData {
  getTemperature()
  getHumidity()
  getPressure()
  measurementsChanged() // 기상 관측값이 갱신되면 해당 메소드가 호출됨
}

```
Weather Data 클래스가 온도, 습도, 기압을 가져오는 get 메소드를 제공하고, 기상 관측값이 갱신되면 `measurementsChanged()` 메소드를 호출한다. <br>
그럼 `measurementsChanged()` 메소드를 통해 3가지 화면을 구현해보자.

<br>

```java
class WeatherData {
  // 인스턴스 변수 선언
  public void measurementsChanged() {
    float temp = getTemperature();
    float humidity = getHumidity();
    float pressure = getPressure();
    
    currentWeatherDisplay.update(temp, humidity, pressure);
    forecastWeatherDisplay.update(temp, humidity, pressure);
    staticsticDisplay.update(temp, humidity, pressure);
  }
}
```

get 메소드들을 이용하여 온도, 습도, 기압을 가져오고 각 화면단에 해당 변수들을 전달하여 `update()`를 하는 방식으로 구현했다.

하지만 이 코드에는 아래와 같은 문제가 발생한다.
1. `currentWeatherDisplay.update`, `forecastWeatherDisplay.update`, `staticsticDisplay.update` 와 같이 구체적인 구현에 맞춰서 코딩했기 때문에, 다른 화면을 추가하고자 한다면 `measurementsChanged()` 메소드를 고치지 않고서는 새로운 화면을 추가할 수 없다.
2. 따라서 바뀔 수 있는 부분(각 화면 단)을 캡슐화 해야한다. 

<br>

### Good Case (Observer 패턴 적용 후)

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/137613203-b335bc4c-adcc-4132-b1cc-d0e5ff8afc1d.jpeg" width="80%">
</p>

1. Subject 인터페이스를 구현하여 WeatherData를 주제 객체로 만들기
```java
interface Subject {
  registerObserver()
  removeObserver()
  notifyObserver()
}

class WeatherData implements Subject {
  registerObserver()
  removeObserver()
  notifyObserver()
  
  getTemperature()
  getHumidity()
  getPressure()
  measurementsChanged()
}
```

2. `현재 날씨, 기상 예보, 기상 통계` 화면에서 구현해야할 인터페이스 정의
```java
interface Observer {
  update() // 새로 갱신된 주제 데이터를 전달하는 인터페이스
}

interface DisplayElement {
  display() // 화면에 표현시키는 인터페이스
}

```

3. 위 인터페이스를 구현한 `현재 날씨, 기상 예보, 기상 통계` 클래스 만들기
```java
class CurrentWeather implements Observer, DisplayElement{
  update()
  display() { // 현재 측정 값을 화면에 표시 }
}

class ForcastWeather implements Observer, DisplayElement{
  update()
  display() { // 기상 예보 표시 }
}

class StatisticsDisplay implements Observer, DisplayElement{
  update()
  display() { // 평균 기온, 평균 습도 등 표시 }
}
```

4. `현재 날씨, 기상 예보, 기상 통계` 화면을 불러오는 메인 함수
```java
public class WeatherStation {

  public static void main(String[] args) {
    WeatherData weatherData = new WeatherData();
    
    // 화면 구성 
    CurrentConditionsDisplay currentDisplay = 
      new CurrentConditionsDisplay(weatherData);
    StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
    ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);
    
    // 기상 측정값 설정
    weatherData.setMeasurements(80, 65, 30.4f);
    weatherData.setMeasurements(82, 70, 29.2f);
    weatherData.setMeasurements(78, 90, 29.2f);
    
    weatherData.removeObserver(forecastDisplay);
    weatherData.setMeasurements(62, 90, 28.1f);
  }
}
```

<br>

만약 새로운 화면 `불쾌지수` 화면을 만든다고 한다면 아래와 같이 쉽게 추가할 수 있다.
```java
class DiscomfortDisplay implements Observer, DisplayElement{
  update()
  display() {
    // 불쾌지수를 화면에 표시
  }
}

public class WeatherStation {
  public static void main(String[] args) {
    WeatherData weatherData = new WeatherData();

    // 기존 화면에 불쾌지수 화면 추가
    ...
    DiscomfortDisplay discomfortDisplay = new DiscomfortDisplay(weatherData);
    
  }
}
```

<br>

## Observer 패턴 적용 
### 목적
하나 이상의 객체에 시스템 내의 다른 객체의 상태 변경 사항을 통지하기 위해서 

<br>

### 사용 용도 
* 객체 간 Loose coupling이 필요할 때
* 하나 이상의 객체의 상태 변화가 다른 객체들의 동작에 영향을 주어야 할 때
* 브로드캐스팅 기능이 요구될 때

+) Observer 패턴은 모델-뷰-컨트롤러(Model-View-controller, MVC) 패러다임과 자주 결합된다. Observer 패턴은 MVC에서 모델과 뷰 사이를 느슨히 연결하기 위해 사용된다. 대표적으로 모델에서 일어나는 이벤트를 통보받는 Observer는 뷰의 내용을 바꾸는 스위치를 작동시킨다.

------

## Strategy

Strategy Pattern은 객체들의 행위를 클래스로 만들어서 캡슐화한 뒤, 행위의 변경이나 수정이 필요할 때 동적으로 행위를 바꿀 수 있도록 하는 디자인 패턴입니다.



<p align="center"><img src="img/Strategy_class.PNG" width="600"></p>



### Strategy Pattern 사용 예



<p align="center"><img src="img/Strategy_robot.PNG" width="400"></p>

```java
public abstract class Robot {
  private String name;
  public Robot(String name) { this.name = name; }
  public String getName() { return name; }
  // 추상 메서드
  public abstract void attack();
  public abstract void move();
}

public class TaekwonV extends Robot {
  public TaekwonV(String name) { super(name); }
  public void attack() { System.out.println("I have Missile."); }
  public void move() { System.out.println("I can only walk."); }
}
public class Atom extends Robot {
  public Atom(String name) { super(name); }
  public void attack() { System.out.println("I have strong punch."); }
  public void move() { System.out.println("I can fly."); }
}
```



```java
public class Client {
  public static void main(String[] args) {
    Robot taekwonV = new TaekwonV("TaekwonV");
    Robot atom = new Atom("Atom");

    System.out.println("My name is " + taekwonV.getName());
    taekwonV.move();
    taekwonV.attack();

    System.out.println()
    System.out.println("My name is " + atom.getName());
    atom.move();
    atom.attack();
  }
}

```



### Atom이 움직이는 방법을 수정한다면?

```java
public class Atom extends Robot {
  public Atom(String name) { super(name); }
  public void attack() { System.out.println("I have strong punch."); }

  //public void move() { System.out.println("I can fly."); }
  public void move() { System.out.println("I can only walk."); } // 수정
}
```

- move()를 수정하려면 기존 코드의 내용을 수정해야 하므로 OCP에 위배됩니다.

- 상속을 통한 메소드 overriding 구현은 중복 코드가 발생하기 쉽습니다.

  - TaekwonV와 Atom의 move() 메서드 내용이 중복됩니다.

  - 새로운 방식으로 수정하려면 중복 코드를 모두 수정해야 합니다.



### 해결 방법

<p align="center"><img src="img/Strategy_strategy.PNG" width="800"></p>

- 변화되는 것을 찾고 이를 클래스로 캡슐화합니다.
  - 변화되는 것: attact(), move()
  - 공격과 이동을 위한 인터페이스를 만들고 이들을 구현한 클래스를 만들어서 캡슐화.
- strategy 멤버 변수를 추가하고 setter로 넣어줍니다.
  - 공격과 이동을 상속받는 것이 아니라 외부 타입의 멤버 변수로 만든다.
  - 상속이 아닌 구성으로 setter를 통해 동적으로 바인딩이 가능하다.
  - Robot에 새로운 공격과 이동 방법을 추가할 때 Robot 구현 클래스를 수정하지 않아도 된다.
  - OCP를 만족

```java
public abstract class Robot {
private String name;
private AttackStrategy attackStrategy;
private MovingStrategy movingStrategy;

public Robot(String name) { this.name = name; }
public String getName() { return name; }
public void attack() { attackStrategy.attack(); }
public void move() { movingStrategy.move(); }

// setter 메서드
public void setAttackStrategy(AttackStrategy attackStrategy) {
  this.attackStrategy = attackStrategy; }
public void setMovingStrategy(MovingStrategy movingStrategy) {
  this.movingStrategy = movingStrategy; }
}

```



```java
public class TaekwonV extends Robot {
	public TaekwonV(String name) { super(name); }
}

public class Atom extends Robot {
	public Atom(String name) { super(name); }
}
```



```java
// 인터페이스
interface AttackStrategy { public void attack(); }

// 구체적인 클래스
public class MissileStrategy implements AttackStrategy {
  public void attack() { System.out.println("I have Missile."); }
}

public class PunchStrategy implements AttackStrategy {
  public void attack() { System.out.println("I have strong punch."); }
}

```



```java
// 인터페이스
interface MovingStrategy { public void move(); }

// 구체적인 클래스
public class FlyingStrategy implements MovingStrategy {
  public void move() { System.out.println("I can fly."); }
}

public class WalkingStrategy implements MovingStrategy {
  public void move() { System.out.println("I can only walk."); }
}
```



```java
public class Client {
	public static void main(String[] args) {
      Robot taekwonV = new TaekwonV("TaekwonV");
      Robot atom = new Atom("Atom");

      /* 수정된 부분: 전략 변경 방법 */
      taekwonV.setMovingStrategy(new WalkingStrategy());
      taekwonV.setAttackStrategy(new MissileStrategy());
      atom.setMovingStrategy(new FlyingStrategy());
      atom.setAttackStrategy(new PunchStrategy());

      /* 아래부터는 동일 */
      System.out.println("My name is " + taekwonV.getName());
      taekwonV.move();
      taekwonV.attack();

      System.out.println()
      System.out.println("My name is " + atom.getName());
      atom.move();
      atom.attack();
	}
}
```

### 장점 및 단점

- 장점
  - 동적으로 Context 의 행위를 변경할 수 있습니다.
  - Context 코드의 변경 없이 새로운 Strategy를 추가하여 수정할 수 있습니다.
    - OCP를 만족합니다.
- 단점
  - Strategy 객체와 Context 객체 사이에 통신 오버헤드가 생깁니다.
    - Context 객채는 사용하지 않는 Strategy 정보도 갖게 됩니다.
  - 객체 수가 증가합니다.

### 실제 사용 예

[Spring framework 에서 oauth2 를 이용하여 google, facebook, 등 로그인을 사용하는 예제](https://kscory.com/dev/design-pattern/strategy)

[스프링 부트 어플리케이션의 전략 패턴](https://velog.io/@hsw0194/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%EC%96%B4%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%EC%9D%98-%EC%A0%84%EB%9E%B5-%ED%8C%A8%ED%84%B4Strategy-Design-Pattern-with-in-Spring-Boot-application)

### Reference

https://gmlwjd9405.github.io/2018/07/06/strategy-pattern.html

https://victorydntmd.tistory.com/292

------

## Visitor

`객체`(데이터 구조)와 `로직`(알고리즘)을 분리하는 디자인 패턴  
새로운 로직을 추가하더라도 객체의 구조는 변경하지 않은 채 새로운 동작을 추가할 수 있다.

### Visitor Pattern을 적용하기 위한 빌드 업

유리컵과 신선 식품을 파는 쇼핑몰 사장님이 됐다고 생각해보자. 눈치 챘겠지만 이 예제에서 **객체**는 **상품**(유리컵, 신선 식품)이 될 것이고, **로직**은 **상품 주문**이 될 것이다.

> - 상품 : 유리컵, 신선 식품
> - 주문 : 포장, 배송, 포인트 적립(추가 기능)

이제 이 예제를 구현하는 과정에서 몇 가지 문제를 짚어보고, 이 문제들을 어떻게 Visitor 패턴으로 해결하는지 알아보자.

<br>

### 문제 1. 유지 보수의 어려움

1. 쇼핑몰에서 판매하는 `상품 인터페이스`를 정의하고 이를 구현하는 `유리컵과 신선 식품 구현체`를 만든다.

```java
/* 상품 인터페이스 */
public interface Product() {
    public void pack();
    public void deliver();
}

/* 유리컵 구현체 */
public class Glass implements Product {
    // 포장
    @Override
    public void pack() { System.out.println("뽁뽁이로 안전하게 포장"); }
    // 배송
    @Override
    public void deliver() {System.out.println("한 달 동안 느긋하게 배송"); }
}
/* 신선 식품 구현체 */
public class FreshGrocery implements Product {
    // 포장
    @Override
    public void pack() { System.out.println("아이스팩으로 신선하게 포장"); }
    // 배송
    @Override
    public void deliver() {System.out.println("하루만에 퀵으로 배송"); }
}
```

[실행]

```java
public static void main(String[] args) {
    Product glass = new Glass();
    Product freshGrocery = new FreshGrocery();

    glass.pack();
    glass.deliver();
    freshGrocery.pack();
    freshGrocery.deliver();
}
```

- 위의 방법으로 구현할 경우 모든 객체마다 로직을 위한 메서드를 정의해야하기 때문에 **1) 새로운 기능을 추가하기 어려**울 뿐만 아니라 **2) 특정 객체에서 로직 구현을 빼먹기도 쉽다**.
- 또한 모든 객체에 대해 기능을 수행하게 하기 위해서는 위에서처럼 main()에서 모든 객체 별 각 함수를 직접 호출해야하기 때문에 **3) iterator를 통한 반복적인 처리가 불가능**하다.

<br>

### 문제 2. 객체 분기의 어려움 및 코드 중복

1번 예제 문제를 해결하기 위해 객체와 기능 로직을 분리해보자.

1. 1번 예제에서 `상품 객체`들은 유지하되, 기능을 위한 메서드는 제거한다.
2. `주문 인터페이스`를 정의한 뒤, 이를 구현한 `세부 기능 구현체`를 정의한다.

[상품 객체]

```java
/* 상품 인터페이스 */
public interface Product() { }

/* 유리컵 구현체 */
public class Glass implements Product { }
/* 신선 식품 구현체 */
public class FreshGrocery implements Product { }
```

[주문 기능]

```java
/* 주문 인터페이스 */
public interface Order {
    public void pack(Product product);
    public void deliver(Product product);
}

/* 주문 구현체 */
public class OrderImpl implements Order {
    // 포장
    @Override
    public void pack(Product product) {
        if (product instanceof Glass) {
            // 유리컵 포장
            System.out.println("뽁뽁이로 안전하게 포장");
        } else if (product instanceof FreshGrocery) {
            // 신선 식품 포장
            System.out.println("아이스팩으로 신선하게 포장");
        }
    }

    // 배송
    @Override
    public void deliver(Product product) {
        if (product instanceof Glass) {
            // 유리컵 배송
            System.out.println("한 달 동안 느긋하게 배송");
        } else if (product instanceof FreshGrocery) {
            // 신선 식품 베송
            System.out.println("하루만에 퀵으로 배송");
        }
    }
}
```

[실행]

```java
public static void main(String[] args) {
    Product glass = new Glass();
    Product freshGrocery = new FreshGrocery();
    Order order = new OrderImpl();

    order.pack(glass);
    order.pack(freshGrocery);
    order.deliver(glass);
    order.deliver(freshGrocery);
}
```

- 위 코드는 1번의 문제점인 반복적인 처리가 가능해졌지만 여전히 문제가 남아있다. 특히 `if (product instanceof Glass)` 부분으로 분기하는 부분이 성에 안찬다.
- 이렇게 객체를 분기할 경우 **1) 특정 객체에 대한 분기를 빼먹을 수도 있**을 뿐만 아니라, **비슷한 구조가 반복되는 코드 중복**이 발생한다.
- 즉, 1번의 문제를 거의 해결하지 못한 격이다.

<br>

### 문제 3. Single Dispatch(Dynamic Dispatch) 문제

### Dispatch란?

`Dispatch`란 메서드를 호출하는 방식을 말한다.

자바는 런타임 시에 어떤 메서드를 호출할지 결정하는, 즉 **런타임** 시에 생성되는 인스턴스를 **동적으로 타입 체크** 하는 `dynamic dispatch`만을 지원한다.(single dispatch)

### 예제

1. 2번 예제의 상품 객체와 주문 클래스를 유지하되, 주문 클래스에서 각 상품 타입 별 메서드를 생성한다.

[주문 기능]

```java
/* 주문 인터페이스 */
public interface Order {
    public void pack(Glass glass);
    public void pack(FreshGrocery freshGrocery);
    public void deliver(Glass glass);
    public void deliver(FreshGrocery freshGrocery);
}

/* 주문 구현체 */
public class OrderImpl implements Order {
    // 유리컵 포장
    @Override
    public void pack(Glass glass) { System.out.println("뽁뽁이로 안전하게 포장"); }
    // 신선 식품 포장
    @Override
    public void pack(FreshGrocery freshGrocery) { System.out.println("아이스팩으로 신선하게 포장"); }
    
    // 유리컵 배송
    @Override
    public void deliver(Glass glass) { System.out.println("한 달 동안 느긋하게 배송"); } 
    // 신선 식품 배송
    @Override
    public void deliver(FreshGrocery freshGrocery) { System.out.println("하루만에 퀵으로 배송"); } 
}
```

[실행]

```java
public static void main(String[] args) {
    Product glass = new Glass();
    Product freshGrocery = new FreshGrocery();
    Order order = new OrderImpl();

    order.pack(glass);
    order.pack(freshGrocery);
    order.deliver(glass);
    order.deliver(freshGrocery);
}
```

- 이번 예제는 2번 예제의 실행 부분인 main() 부분이 동일하다. 언뜻보면 가능해 보일지라도 이번에는 `컴파일 에러`가 발생하게 된다.
- `OrderImpl` 클래스 내부의 `pack()` 메서드와 `deliver()` 메서드는 각각 서로 다른 매개변수 타입(`Glass`, `FreshGrocery`)으로 정의하여 **오버로딩**하고 있다.
- 하지만 실행 부분에서는 `glass`와 `freshGrocery` 인스턴스 모두 `Product` 타입의 객체를 생성하여 사용하고 있다. 따라서 실제로는 `Glass` 타입과 `FreshGrocery` 타입의 인스턴스일지라도, **Dynamic Dispatch** 특성 상 컴파일 시 `Glass` 타입과 `FreshGrocery` 타입으로 인식될 수 없다.
- 더 작은 타입에 더 큰 타입의 객체를 넣을 수 없다.
- 이 문제를 해결하자고 `glass`와 `freshGrocery` 인스턴스를 `Product` 타입이 아닌 `Glass` 타입과 `FreshGrocery` 타입으로 생성하게 된다면 이는 더 이상 인터페이스를 사용하는 의미가 없는 코드가 되어버린다.

<br>

### Visitor Pattern을 이용한 해결 방안

이제 위의 문제점들을 Visitor Pattern을 이용해 해결해보자. 

1. 1번 문제를 해결하기 위해 **객체와 로직을 분리**할 것이다.
2. 또한 2번 문제를 해결하기 위해 **각 객체를 매개변수로 하는 기능 메서드를 각각 정의**한다.
3. 마지막으로 **Double Dispatch**를 구현하여 3번 문제를 해결한다!

[상품 객체]

```java
/* 상품 인터페이스 */
public interface Product() {
    public void order(Order order);
}

/* 유리컵 구현체 */
public class Glass implements Product {
    @Override
    public void order(Order order) {
        order.placeOrder(this);
    }
}
/* 신선 식품 구현체 */
public class FreshGrocery implements Product {
    @Override
    public void order(Order order) {
        order.placeOrder(this);
    }
}
```

[주문 기능]

```java
/* 주문 인터페이스 */
public interface Order {
    public void placeOrder(Glass glass);
    public void placeOrder(FreshGrocery freshGrocery);
}

/* 포장 주문 구현체 */
public class PackOrder implements Order {
    // 유리컵 포장
    @Override
    public void placeOrder(Glass glass) { System.out.println("뽁뽁이로 안전하게 포장"); }

    // 신선 식품 포장
    @Override
    public void placeOrder(FreshGrocery freshGrocery) { System.out.println("아이스팩으로 신선하게 포장"); }
}

/* 배송 주문 구현체 */
public class DeliverOrder implements Order {
    // 유리컵 배송
    @Override
    public void placeOrder(Glass glass) { System.out.println("한 달 동안 느긋하게 배송"); } 

    // 신선 식품 배송
    @Override
    public void placeOrder(FreshGrocery freshGrocery) { System.out.println("하루만에 퀵으로 배송"); } 
}

/* 포인트 적립 주문 구현체 */
public class pointSaveOrder implements Order {
    // 유리컵 포인트 적립
    @Override
    public void placeOrder(Glass glass) { System.out.println("구매 금액의 10% 적립"); } 

    // 신선 식품 포인트 적립
    @Override
    public void placeOrder(FreshGrocery freshGrocery) { System.out.println("구매 금액의 5% 적립"); } 
}
```

[실행]

```java
public static void main(String[] args) {
    Product glass = new Glass();
    Product freshGrocery = new FreshGrocery();
    Order packOrder = new PackOrder();
    Order deliverOrder = new DeliverOrder();

    glass.order(packOrder);
    glass.order(deliverOrder);
    freshGrocery.order(packOrder);
    freshGrocery.order(deliverOrder);
}
```

Visitor 패턴을 적용한 이후 달라진 부분은 다음과 같다.

- 상품 객체 내에서 주문 기능을 구현하지 않아도 된다.
- 주문 기능을 클래스로 빼낸 뒤, 각 상품 객체 타입 별 별도의 메서드를 정의하고 있기 때문에 구현을 빼먹을 위험이 적다.
- 각 상품 타입 객체에서 기능 함수를 호출하여 사용하고 있기 때문에 `single dispatch`문제가 발생하지 않는다. (더 큰 타입에서 더 작은 타입의 객체를 받고 있기 때문에)
- 새로운 주문 기능을 추가하기가 쉽다! 객체에서는 변경 없이 오직 주문 클래스만 추가하면 된다.

추가적으로 Visitor Pattern에서 흔히 말하는 Visitor, Element의 역할은 다음과 같이 매칭될 수 있다.

> - Visitor : 기능이나 로직을 위한 인터페이스 ex) `Order 인터페이스`
> - ConcreteVisitor : 기능이나 로직 구현체 ex) `PackOrder 클래스`, `DeliverOrder 클래스`, `pointSaveOrder 클래스`
> - visit(Element) : 객체에서 호출되어 사용하기 위해 기능 클래스가 공통적으로 구현해야 할 메서드 ex) `placeOrder()`
> - Element : 기능이나 로직을 사용할 객체 인터페이스 ex) `Product 인터페이스`
> - ConcreteElement : 객체 인터페이스의 구현체 ex) `Glass 클래스`, `FreshGrocery 클래스`
> - accept(Visitor) : 기능 클래스 내의 visit 메서드를 호출하기 위해 모든 객체가 구현해야 할 메서드 ex) `order()`

<br>
<hr>

### References

[방문자 패턴 - Visitor pattern by Jeongjin Kim](https://thecodinglog.github.io/design/2019/10/29/visitor-pattern.html)

[토비의봄#01. Double Dispatch by LichKing](https://multifrontgarden.tistory.com/133)

