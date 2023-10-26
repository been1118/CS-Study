# 이터레이터 패턴 (iterator pattern)

<hr>

- 컬렉션 구현 방법을 노출시키지 않으면서도 그 집합체안에 들어있는 모든 항목에 접근할 수 있게 해 주는 방법을 제공해 주는 패턴
- 이터레이터 패턴을 사용하면 집합체 내에서 어떤 식으로 일이 처리되는지 몰라도 그 안에 들어있는 항목들에 대해서 반복작업을 수행 할 수 있다.


## 예제

<hr>
명단에 이름을 넣고 그 이름을 확인하는 하나씩 예제


```Java
public class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```
- 이름에 대한 정보를 갖고있는 클래스

<br>

```Java
public interface Aggregate {

    Iterator createIterator();
}
```

- 집합체를 의미하는 인터페이스
- Aggregate는 Iterator 역할을 만들어내는 인터페이스를 결정한다.

<br>

```Java
public class NameList implements Aggregate {
    private Person[] people; 
    private int last = 0; // 명단의 마지막 이름의 위치

    public NameList(int size) {
        people = new Person[size];
    }

    public Person getPerson(int index) {
        return people[index];
    }

    public int getLength() {
        return last;
    }

    // 명단에 이름 추가
    public void appendPerson(Person person) {
        if (last < people.length) {
            this.people[last] = person;
            last++;
        } else {
            System.out.println("명단 공간 부족");
        }
    }

    @Override
    public Iterator createIterator() {
        return new NameListIterator(this);
    }
}
```

- 명단 클래스
- NameList는 이러한 Aggregate의 구현체이다.
- NameList안을 돌아다닐 Iterator를 생성하고 명단을 관리하는 역할을 한다.

<br>

```Java
public class NameListIterator implements Iterator<Person> {
    private NameList nameList; // 검색을 수행할 명단
    private int index = 0; // 현재 처리할 이름의 위치

    public NameListIterator(NameList nameList) {
        this.nameList = nameList;
    }

    @Override
    public boolean hasNext() {
        return index < nameList.getLength();
    }

    @Override
    public Person next() {
        Person person = nameList.getPerson(index);
        index++;
        return person;
    }
}
```

- 명단에서 검색을 수행하는 클래스
- NameListIterator에서 Iterator를 다루기 위해 Iterator 인터페이스를 상속받는다.

<br>

```Java
public class Main {
    public static void main(String[] args) {
        NameList nameList = new NameList(10);
        
        Person person1 = new Person("김");
        Person person2 = new Person("이");
        Person person3 = new Person("박");
        
        nameList.appendPerson(person1);
        nameList.appendPerson(person2);
        nameList.appendPerson(person3);

        System.out.println("현재 들어있는 이름 수 : " + nameList.getLength());

        Iterator it = nameList.createIterator();

        while (it.hasNext()) {
            Person person = (Person) it.next();
            System.out.println(person.getName());
        }
    }
}
/*
 현재 들어있는 이름 수 : 3
 김
 이
 박
*/
```

- NameListIterator는 명단에서 이름을 한개씩 뽑아오는 역할을 한다.
- 검색할 이름이 존재하는 동안 while문이 수행되고, it.next()에 의해 이름을 한개씩 출력한다.

## 장점

<hr>

- 일관된 이터레이터 인터페이스를 사용해 여러 형태의 컬렉션에 대해 동일한 순회 방법을 제공한다.
- 집합체 내에서 어떤 식으로 일이 처리되는지 알 필요 없이, 집합체 안에 들어있는 모든 항목에 접근 할 수 있게 해준다.
- 모든 항목에 일일이 접근하는 작업을 컬렉션 객체가 아닌 이터레이터 객체에서 맡게 된다. 
이렇게 하면, 집합체의 인터페이스 및 구현이 간단해질 뿐만 아니라, 
집합체에서는 반복 작업에서 손을 떼고 원래 자신이 할 일에만 전념할 수 있다.
- 집합체의 구현과 접근하는 처리 부분을 반복자 객체로 분리해 결합도를 줄 일 수 있다.

## 단점

<hr>

- 단순한 순회를 구현하는 경우 클래스만 많아져 복잡도가 증가할 수 있다.