# 프록시 패턴과 프록시 서버

<hr>

## 프록시 패턴

<hr>

프록시 패턴(Proxy Pattern)은 대상 원본 객체를 대리하여 대신 처리하게 함으로써 로직의 흐름을 제어하는 행동 패턴이다.

클라이언트가 대상 객체를 직접 쓰는게 아니라 중간에 프록시(대리인)을 거쳐서 쓰는 코드 패턴이라고 보면 된다. 

따라서 대상 객체(Subject)의 메소드를 직접 실행하는 것이 아닌, 
대상 객체에 접근하기 전에 프록시(Proxy) 객체의 메서드를 접근한 후 추가적인 로직을 처리한뒤 접근하게 된다.


그냥 객체를 이용하면 되지, 이렇게 번거롭게 중계 대리자를 통해 이용하는 방식을 취하는 이유는, 
대상 클래스가 민감한 정보를 가지고 있거나 인스턴스화 하기에 무겁거나 추가 기능을 가미하고 싶은데, 
원본 객체를 수정할수 없는 상황일 때를 극복하기 위해서 이다. 

대체적으로 정리하자면 다음과 같은 효과를 누릴수 있다고 보면 된다.

- 보안(Security) : 프록시는 클라이언트가 작업을 수행할 수 있는 권한이 있는지 확인하고 검사 결과가 긍정적인 경우에만 요청을 대상으로 전달한다.
- 캐싱(Caching) : 프록시가 내부 캐시를 유지하여 데이터가 캐시에 아직 존재하지 않는 경우에만 대상에서 작업이 실행되도록 한다.
- 데이터 유효성 검사(Data validation) : 프록시가 입력을 대상으로 전달하기 전에 유효성을 검사한다.
- 지연 초기화(Lazy initialization) : 대상의 생성 비용이 비싸다면 프록시는 그것을 필요로 할때까지 연기할 수 있다.
- 로깅(Logging) : 프록시는 메소드 호출과 상대 매개 변수를 인터셉트하고 이를 기록한다.
- 원격 객체(Remote objects) : 프록시는 원격 위치에 있는 객체를 가져와서 로컬처럼 보이게 할 수 있다.

## 프록시 패턴 종류

<hr>

프록시는 다른 객체에 대한 접근을 제어하는 개체이다. 여기서 다른 객체를 대상(Subject)라고 부른다. 
프록시와 대상은 동일한 인터페이스를 가지고 있으며 이를 통해 다른 인터페이스와 완전히 호환되도록 바꿀수 있다.

### 기본형 프록시 (Normal Proxy)

```Java
interface ISubject {
    void action();
}

class RealSubject implements ISubject {

    @Override
    public void action() {
        System.out.println("원본 객체");
    }
}
```

```Java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 구성

    Proxy(RealSubject subject) {
        this.subject = subject;
    }

    @Override
    public void action() {
        subject.action(); // 위임
        // do something
        System.out.println("프록시 객체");
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy(new RealSubject());
        sub.action();
        // 원본 객체
        // 프록시 객체
    }
}
```

### 가상 프록시 (Virtual Proxy)

- 지연 초기화 방식
- 가끔 필요하지만 항상 메모리에 적재되어 있는 무거운 서비스 객체가 있는 경우
- 이 구현은 실제 객체의 생성에 많은 자원이 소모 되지만 사용 빈도는 낮을 때 쓰는 방식이다.
- 서비스가 시작될 때 객체를 생성하는 대신에 객체 초기화가 실제로 필요한 시점에 초기화될수 있도록 지연할 수 있다.

```Java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 구성

    Proxy() {
    }

    @Override
    public void action() {
    	// 프록시 객체는 실제 요청(action(메소드 호출)이 들어 왔을 때 실제 객체를 생성한다.
        if(subject == null){
            subject = new RealSubject();
        }
        subject.action(); // 위임
        // do something
        System.out.println("프록시 객체");
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy();
        sub.action();
    }
}
```

### 보호 프록시 (Protection Proxy)

- 프록시가 대상 객체에 대한 자원으로의 엑세스 제어(접근 권한)
- 특정 클라이언트만 서비스 객체를 사용할 수 있도록 하는 경우
- 프록시 객체를 통해 클라이언트의 자격 증명이 기준과 일치하는 경우에만 서비스 객체에 요청을 전달할 수 있게 한다.

```Java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 구성
    boolean access; // 접근 권한

    Proxy(RealSubject subject, boolean access) {
        this.subject = subject;
        this.access = access;
    }

    @Override
    public void action() {
        if(access) {
            subject.action(); // 위임
            // do something
            System.out.println("프록시 객체");
        }
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy(new RealSubject(), false);
        sub.action();
    }
}
```

### 로깅 프록시 (Logging Proxy)

- 대상 객체에 대한 로깅을 추가하려는 경우
- 프록시는 서비스 메서드를 실행하기 전달하기 전에 로깅을 하는 기능을 추가하여 재정의한다.

```Java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 구성

    Proxy(RealSubject subject) {
        this.subject = subject;
    }

    @Override
    public void action() {
        log.info("logging");
        
        subject.action(); // 위임
        // do something
        System.out.println("프록시 객체");

        log.info("logging");
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy(new RealSubject());
        sub.action();
    }
}
```

### 캐싱 프록시 (Caching Proxy)

- 데이터가 큰 경우 캐싱하여 재사용을 유도
- 클라이언트 요청의 결과를 캐시하고 이 캐시의 수명 주기를 관리

```Java
class Proxy implements ISubject {
    private RealSubject subject; // 대상 객체를 구성

    Proxy(RealSubject subject) {
        this.subject = subject;
    }

    @Override
    public void action() {
        if(subject == null){
            subject = new RealSubject(); // 처음 한 번만 생성
        }
        subject.action(); // 위임
        // do something
        System.out.println("프록시 객체");
    }
}

class Client {
    public static void main(String[] args) {
        ISubject sub = new Proxy(new RealSubject());
        sub.action();
    }
}
```

## 프록시 패턴 정리

<hr>

### 패턴 사용 시기

- 접근을 제어하거가 기능을 추가하고 싶은데, 기존의 특정 객체를 수정할 수 없는 상황일때
- 초기화 지연, 접근 제어, 로깅, 캐싱 등, 기존 객체 동작에 수정 없이 추가하고 싶을 때

### 장점

- 단일 책임 원칙(SRP) 준수
  - 대상 객체는 자신의 기능에만 집중 하고, 그 이외 부가 기능을 제공하는 역할을 프록시 객체에 위임하여 다중 책임을 회피 할 수 있다.
- 개방 폐쇄 원칙(OCP) 준수
  - 기존 대상 객체의 코드를 변경하지 않고 새로운 기능을 추가할 수 있다.
- 원래 하려던 기능을 수행하며 그외의 부가적인 작업(로깅, 인증, 네트워크 통신 등)을 수행하는데 유용하다.
- 클라이언트는 객체를 신경쓰지 않고, 서비스 객체를 제어하거나 생명 주기를 관리할 수 있다.
- 사용자 입장에서는 프록시 객체나 실제 객체나 사용법은 유사하므로 사용성에 문제 되지 않는다.
 
### 단점

- 많은 프록시 클래스를 도입해야 하므로 코드의 복잡도가 증가한다.
- 프록시 클래스 자체에 들어가는 자원이 많다면 서비스로부터의 응답이 늦어질 수 있다.

## 프록시 서버

<hr>

프록시 서버는 클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해 주는 컴퓨터 시스템이나 응용 프로그램을 가리킨다.

보통 웹은 클라이언트에서 서버로, 서버에서 클라이언트로 통신하며 데이터를 전달한다.
이때 필연적으로 중복되는 데이터를 반복하여 전달하는 상황이 발생하는데, 
이렇게 동일한 요청을 매번 처리하는 것은 곧 리소스 낭비 와 서버의 부하 로 이어지게 된다.

때문에 본 서버에 도달하기 전에 새로운 서버(proxy server)를 미리 배치하여 중복 요청에 대해(연산이 필요없는) 동일한 응답을 할 수 있다면,
클라이언트에겐 빠른 속도의 서비스를, 서버에게는 불필요한 부하를 줄이는 효과를 낼 수 있게 된다.


## 프록시의 두 종류

<hr>

프록시 서버는 네트워크 상 어디에 위치하느냐, 
혹은 어느 방향으로 데이터를 제공하느냐에 따라 Forward Proxy 와 Reverse Proxy 로 나뉘게 된다.


## 포워드 프록시 (Forward Proxy)

<hr>

프록시 서버는 클라이언트 바로 뒤에 놓여 있다.

같은 내부망에 존재하는 클라이언트의 요청을 받아 인터넷을 통해 외부 서버에서 데이터를 가져와 클라이언트에게 응답해준다.

즉, 클라이언트가 서버에 접근하고자 할때, 클라이언트는 타겟 서버의 주소를 포워드 프록시에 전달하여, 
포워드 프록시가 인터넷으로 요청된 내용을 가져오는 방식이다.

### 포워드 프록시 사용 이점

1. 클라이언트 보안 (Security)
> 포워드 프록시 서버는 방화벽과 같은 개념으로 제한을 위해 사용 된다고 보면 된다.

2. 캐싱 (Caching)
> 어떤 웹 페이지에 접근하면 프록시 서버는 해당 페이지 서버의 정보를 캐싱(임시보관)해둔다.
> 
> 그래서 똑같이 해당 페이지에 접근하거나, 다른 클라이언트가 해당 페이지를 요청할 때 , 
> 캐시된 정보(페이지)를 그대로 반환할 수 있고, 이는 서버의 부하를 줄이는 효과도 낼 수 있다.

3. 암호화 (Encryption)
> 클라이언트의 요청은 포워드 프록시 서버를 통과할 때 암호화된다.
> 
> 암호화된 요청은 다른 서버를 통과할 때 필요한 최소한의 정보만 갖게 되는데, 
> 이는 클라이언트의 ip 를 (보안을 위해) 감춰주는 보안 효과를 내준다.

## 리버스 프록시 (Reverse Proxy)

<hr>

리버스 프록시는 웹서버/WAS 앞에 놓여 있는 것을 말한다.

클라이언트는 웹서비스에 접근할때 웹서버에 요청하는 것이 아닌, 
프록시로 요청하게 되고, 프록시가 배후(reverse)의 서버로부터 데이터를 가져오는 방식이다.

클라이언트쪽으로 데이터(response)를 밀어주는게 포워드라면, 
그 반대편인 서버 쪽으로 데이터(request)를 밀어주는 것이 리버스 프록시 라고 보면 된다.

### 리버스 프록시 사용 이점

1. 로드 밸런싱 (Load Balancing)
> 리버스 프록시 서버를 여러개의 본 서버들 앞에 두면 특정 서버가 과부화 되지 않게 로드밸런싱이 가능하다.

2. 서버 보안 (Security)
> 리버스 프록시를 사용하면 본래 서버의 IP 주소를 노출시키지 않을 수 있다. 
> 따라서 DDoS 공격과 같은 공격을 막는데 유용하다.

3. 캐싱 (Caching)
> 포워드 프록시의 캐싱과 비슷한 기능을 한다고 보면 된다. (정확히 프록시의 본래 기능)

4. 암호화 (Encryption)
> SSL 암호화에도 좋다.
> 
> 본래 서버가 클라이언트들과 통신을 할때 SSL(or TSL)로 암호화, 복호화를 할 경우 비용이 많이 들게 된다.
그러나 리버스 프록시를 사용하면 들어오는 요청을 모두 복호화하고 나가는 응답을 암호화해주므로
> 클라이언트와 안전한 통신을 할수 있으며 본래 서버의 부담을 줄여줄 수 있다.



