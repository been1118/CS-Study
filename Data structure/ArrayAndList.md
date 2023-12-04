# 배열과 리스트

<hr>

## 공통점

<hr>

1. 동일한 특성의 데이터들을 묶는다.
2. 반복문(loop)내에 변수를 이용하여 하나의 묶음 데이터들을 모두 접근할 수 있다.

## 차이점

<hr>

배열
1. 처음 선언한 배열의 크기(길이)는 변경할 수 없다. 이를 정적 할당(static allocation)이라고 한다.
2. 메모리에 연속적으로 나열되어 할당된다.
3. index에 위치한 하나의 데이터(element)를 삭제하더라도 해당 index에는 빈공간으로 계속 남는다.

리스트

1. 리스트의 길이가 가변적이다. 이를 동적 할당(dynamic allocation)이라고 한다.
2. 데이터들이 연속적으로 나열된다. (메모리에 연속적으로 나열되지 않고 각 데이터들은 주소(reference)로 연결되어있다.)
3. 데이터(element) 사이에 빈 공간을 허용하지 않는다.

## 배열의 장단점

<hr>

<장점>

1. 데이터 크기가 정해져있을 경우 메모리 관리가 편하다.

2. 메모리에 연속적으로 나열되어 할당하기 때문에 index를 통한 색인(접근)속도가 빠르다.



<단점>

1. 배열의 크기를 변경할 수 없기 때문에 초기에 너무 큰 크기로 설정해주었을 경우 메모리 낭비가 심해지고, 
반대로 너무 작은 크기로 설정해주었을 경우 데이터를 다 못담을 수 있는 경우가 발생 할 수 있다.

2. 데이터를 삽입(add), 삭제(remove)를 할 때 빈 공간을 허용하지 않고자 한다면, 
뒤의 데이터들을 모두 밀어내거나 당여주어야 하기 때문에 속도가 느려 삽입, 삭제가 빈번한 경우에는 유용하지 않다.

## 리스트의 장단점

<장점>

1. 데이터의 개수에 따라 해당 개수만큼 메모리를 동적 할당해주기 때문에 메모리 관리가 편리해진다.
2. 빈 공간을 허용하지 않기 때문에 데이터 관리에도 편하다.
3. 포인터(주소)로 각 데이터들이 연결되어 있기 때문에 해당 데이터에 연결된 주소만 바꿔주면 되기 때문에 삽입 삭제에 용이하다.(ArrayList는 예외)

<단점>

1. 객체로 데이터를 다루기 때문에 적은양의 데이터만 쓸 경우 배열에 비해 차지하는 메모리가 커진다.
간단히 예로들어 primitive type인 Int는 4Byte를 차지한다. 반면에 Wraaper class인 Integer는 32bit JVM에선 객체의 헤더(8Byte), 원시 필드(4Byte), 패딩(4Byte)으로 '최소 16Byte를 차지한다. 거기에다가 이러한 객체데이터들을 다시 주소로 연결하기 때문에 16 + α 가 된다.
2. 기본적으로 주소를 기반으로 구성되어있고 메모리에 순차적으로 할당하는 것이 아니기 때문에(물리적 주소가 순차적이지 않다) 색인(검색)능력은 떨어진다.

# in Effective Java

<hr>

## 배열과 리스트의 차이

<hr>

### 1. 배열은 공변(covariant)이고, 리스트(제네릭)는 불공변(invariant)이다.

> - 공변
>   - Sub가 Super의 하위 타입이라면 배열 Sub[]은 배열 Super[] 하위 타입이 된다.(함께 변한다.)
> - 불공변?
>   - List<Sub>는 List<Super>의 하위 타입도 아니고 상위 타입도 되지 않는다.

이 둘의 특징을 보고 배열 좋은데? 제네릭 문제 있는거 아니야? 라고 할 수 있으나,

문제가 있는건 배열이다.

아래 코드는 문법상 허용되는 코드이다.

```Java
Object[] objectArray = new Long[1];
objectArray[0] = "문자열";
```

하지만 런타임에 실패한다.

![image](https://github.com/been1118/CS-Study/assets/123082067/b4c49952-c4da-43a4-86c4-74a8d09d7383)

아래 코드는 호환되지 않는 타입이기 때문에 컴파일 단계에서 에러가 발생한다.

```Java
List<Object> objectList = new ArrayList<Long>();
objectList.add("문자열");
```

![image](https://github.com/been1118/CS-Study/assets/123082067/97547e5f-3fb9-4615-9399-3f7d3058b78d)

### 2. 배열은 실체화(reify)된다. 

- 실체화?

배열은 런타임에도 담기로 한 원소의 타입을 확인한다.
반면, 제네릭은 타입 정보가 런타임에는 소거(erasure)된다. 
원소 타입을 컴파일타임에만 검사하며 런타임에는 알수조차 없다.

이처럼 배열과 제네릭은 잘 어우러지지 못한다.

예로 배열은 제네릭 타입으로 사용 할 수 없다.

```Java
// 배열은 아래와 같이 사용하면 오류가 발생한다.
new List<E>[]; // 제네릭 타입
new List<String>[]; // 매개변수화 타입
new E[]; // 타입 매개변수
```

왜 제네릭 배열의 생성을 막았을까?

타입 안전성이 보장되지 않기 때문입니다. 
제네릭 배열의 생성을 허용한다면 컴파일러가 자동으로 생성한 형변환 코드에서 런타임 시점의 ClassCastException이 발생할 수 있다.

런타임 시점의 형변환 예외가 발생하는 것을 막겠다는 제네릭의 취지에 맞지 않다. 
아래 예시의 (1) 번처럼 제네릭 배열이 생성된다고 가정해보자.

```Java
List<String>[] stringLists = new List<String>[1]; // (1) 
List<Integer> intList = List.of(42);              // (2)
Object[] objects = stringLists;                   // (3)
objects[0] = intList;                             // (4)
String s = stringLists[0].get(0);                 // (5)
```

- (2) 번은 원소가 하나인 List를 생성한다.
- (3) 번은 (1)번 과정에서 생성된다고 가정한 제네릭 배열을 Object[]에 할당한다. 배열은 공변(Covariant)이므로 아무런 문제가 없다.
- (4) 번은 (2)에서 생성한 List<Integer>의 인스턴스를 Object 배열의 첫 원소로 저장한다. 제네릭은 런타임 시점에서 타입이 사라지므로 List<Integer>은 List가 되고 List<Integer>[]는 List[]가 된다. 따라서 ArrayStoreException이 발생하지 않는다.
- 문제되는 부분은 (5) 번이다. List<String> 인스턴스만 담겠다고 선언한 stringLists 배열에 다른 타입의 인스턴스가 담겨있는데 첫 원소를 꺼내려고 한다. 그리고 이를 String으로 형변환하는데, 이 원소는 Integer 타입이므로 런타임에 ClassCastException 이 발생한다.

### 실체화 불가 타입

`E`, `List<E>`, `List<String>` 같은 타입을 실체화 불가 타입(non-reifiable type)이라 한다.

제네릭 소거로 인해 실체화되지 않아서 런타임 시점에 컴파일타임보다 타입 정보를 적게 가지는 타입을 말한다.

## 배열로 형변환시 오류가 발생한다면

<hr>

```Java
public class Chooser {
    private final Object[] choiceArray;
    
    public Chooser(Collection choices) {
        this.choiceArray = choices.toArray();
    }
    
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

이 클래스의 `choose()` 메서드 사용한다면 반환된 `Object`를 원하는 타입으로 형변환해야 한다.

다른 타입의 원소가 들어 있다면 런타임 에러가 발생할 것이다.

```Java
public class Chooser<T> { // 수정
    private final T[] choiceArray; // 수정

    public Chooser(Collection<T> choices) { // 수정
        this.choiceArray = (T[]) choices.toArray();
    }

    // choose 메소드는 동일하다.
}
```

제네릭 타입으로 수정한 코드이다.

이번엔 경고가 뜬다.

![image](https://github.com/been1118/CS-Study/assets/123082067/a362eba3-a1e0-43b9-adee-ac953bb155d0)

T가 무슨 타입인지 알 수 없으니 이 형변환이 런타임에도 안정할지 보장할 수 없다고 컴파일러가 알려주는 것이다.

하지만 프로그램 실행 자체는 정상적으로 동작한다. 단지 형변환의 안전을 보장하지 못할 뿐이다.

코드를 작성한 사람이 안전하다고 확신하면 주석과 애너테이션을 통해 경고를 숨겨도 된다.
하지만 애초에 경고의 원인을 제거하는 편이 훨씬 낫다.

비검사 형변환 경고를 제거하려면 배열 대신 리스트를 쓰면 된다.

```Java
class Chooser<T> {
    private final List<T> choiceList; // 수정

    public Chooser(Collection<T> choices) {
        this.choiceList = new ArrayList<>(choices); // 수정
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size())); // 수정
    }
}
```

이제 Chooser는 에러나 경고 없이 컴파일 되며 ClassCastException을 만날 일이 없다.

## 결론

<hr>

- 배열은 공변이고 실체화되는 반면, 리스트(제네릭)는 불공변이고 타입 정보가 소거된다.
- 배열은 런타임에는 타입 안전하지만 컴파일타임에는 그렇지 않다.
- 둘을 섞어 쓰다가 오류나 경고를 만나면, 배열을 리스트로 대체하는 방법을 적용해보자.