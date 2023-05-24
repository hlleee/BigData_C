# 2. 가비지 컬렉션 (Garbage Collection)

## ❓가비지 컬렉션(Garbage Collection, GC)이란?
: 메모리 관리 기법 중 하나로 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역을 해제하는 기능이다. 
즉, 동적 할당된 메모리 영역 가운데 어떤 변수도 가리키지 않는 메모리 영역을 탐지하여 자동으로 해제하는 기법이다. 
> 
- 가비지 컬렉션은 프로그램에서 더 이상 사용하지 않는 메모리를 자동으로 정리하는 것이다.<br/>
- 비워줘야 할 메모리 공간을 남겨두지 않고 메모리를 꽉채우게 되면, 메모리 누수(memory leak)가 발생한다.<br/>
- 메모리 누수로 속도저하, 예기치 못한 종료, 느린 응답속도 등과 같이 많은 문제가 발생할 수 있다.<br/>
- 메모리 누수가 발생하지 않게 프로그래머는 이를 방지하고 버그가 있으면 잡아야한다.<br/>
- 가비지 컬렉터는 프로그래밍 언어에서 프로그래머가 해야할 위와 같은 일을 대신해준다.<br/>
- 메모리를 알아서 관리하는 언어들을 managed language라고 한다. (반대: unmanaged language)<br/>
- 특정 때에 특정 방식으로 필요없는 정보들 즉, 가비지들을 쓸어담아 버린다.<br/>
- 단, 지워야할 것들을 100 퍼센트 잡아내지는 못한다.<br/>
- 좋은 프로그래머가 되려면 메모리 관리에도 신경을 써야 한다.
<br/>

#### 🤬가비지 발생⁉️
```java
Person person = new Person();
person.setName("Mang");
person = null;

// 가비지 발생
person = new Person();
person.setName("MangKyu");
```
-> 기존의 Mang으로 생성된 person 객체는 더이상 참조를 하지 않고 사용이 되지 않아서 Garbage(가비지)가 되었다. Java나 Kotlin에서는 이러한 메모리 누수를 방지하기 위해 가비지 컬렉터(Garbage Collector, GC)가 주기적으로 검사하여 메모리를 청소해준다.

(물론 Java에서도 System.gc()를 이용해 호출할 수 있지만, 해당 메소드를 호출하는 것은 시스템의 성능에 매우 큰 영향을 미치므로 절대 호출해서는 안된다.)

<br/>

**[ Minor GC와 Major GC ]**
JVM의 Heap영역은 처음 설계될 때 다음의 2가지를 전제(Weak Generational Hypothesis)로 설계되었다.<br/>

대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
오래된 객체에서 새로운 객체로의 참조는 아주 적게 존재한다.
 <br/>

즉, 객체는 대부분 일회성되며, 메모리에 오랫동안 남아있는 경우는 드물다는 것이다. 그렇기 때문에 객체의 생존 기간에 따라 물리적인 Heap 영역을 나누게 되었고 Young, Old 총 2가지 영역으로 설계되었다. 초기에는 Perm 영역이 존재하였지만 Java8부터 제거되었다<br/>
![](https://velog.velcdn.com/images/hllee/post/de93c33c-71b4-440f-9f54-2a22a6752e1d/image.png)

<br/>

#### Young 영역(Young Generation)
: 새롭게 생성된 객체가 할당(Allocation)되는 영역<br/>
- 대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라진다.<br/>
- Young 영역에 대한 가비지 컬렉션(Garbage Collection)을 Minor GC라고 부른다.<br/>

<br/>

#### Old 영역(Old Generation)<br/>
: Young영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역<br/>
- Young 영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다.<br/>
- Old 영역에 대한 가비지 컬렉션(Garbage Collection)을 Major GC라고 부른다.<br/>

Old 영역이 Young 영역보다 크게 할당되는 이유는 Young 영역의 수명이 짧은 객체들은 큰 공간을 필요로 하지 않으며 큰 객체들은 Young 영역이 아니라 바로 Old 영역에 할당되기 때문이다.
<br/>

예외적인 상황으로 Old 영역에 있는 객체가 Young 영역의 객체를 참조하는 경우도 존재할 것이다. 이러한 경우를 대비하여 Old 영역에는 512 bytes의 덩어리(Chunk)로 되어 있는 카드 테이블(Card Table)이 존재한다.

![](https://velog.velcdn.com/images/hllee/post/7ed8eb07-daa4-42c5-ad76-0e37e3b25191/image.png)

카드 테이블에는 Old 영역에 있는 객체가 Young 영역의 객체를 참조할 때 마다 그에 대한 정보가 표시된다. 카드 테이블이 도입된 이유는 간단한다. Young 영역에서 가비지 컬렉션(Minor GC)가 실행될 때 모든 Old 영역에 존재하는 객체를 검사하여 참조되지 않는 Young 영역의 객체를 식별하는 것이 비효율적이기 때문이다. 그렇기 때문에 Young 영역에서 가비지 컬렉션이 진행될 때 카드 테이블만 조회하여 GC의 대상인지 식별할 수 있도록 하고 있다.
<br/>


### 왜 필요하고?
- 불필요한 메모리를 알아서 정리해줌으로 메모리 공간의 효율성 향상.
<br/>

### Garbage Collection(가비지 컬렉션)의 동작 방식

Young 영역과 Old 영역은 서로 다른 메모리 구조로 되어 있기 때문에, 세부적인 동작 방식은 다르다. 하지만 기본적으로 가비지 컬렉션이 실행된다고 하면 다음의 2가지 공통적인 단계를 따르게 된다.
<br/>

#### 1. Stop The World
![](https://velog.velcdn.com/images/hllee/post/64b736ef-3737-4c03-84c1-1cfdc21c0a35/image.png)

Stop The World는 가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 실행을 멈추는 작업이다. GC가 실행될 때는 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개된다. 당연히 모든 쓰레드들의 작업이 중단되면 애플리케이션이 멈추기 때문에, GC의 성능 개선을 위해 튜닝을 한다고 하면 보통 stop-the-world의 시간을 줄이는 작업을 하는 것이다. 또한 JVM에서도 이러한 문제를 해결하기 위해 다양한 실행 옵션을 제공하고 있다.<br/>

<br/>

#### 2. Mark and Sweep <br/>
- Mark: 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업<br/>
- Sweep: Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업<br/>

![](https://velog.velcdn.com/images/hllee/post/477165b3-e2db-432d-8155-b80de2841398/image.png)


Stop The World를 통해 모든 작업을 중단시키면, GC는 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 각각이 어떤 객체를 참고하고 있는지를 탐색하게 된다. 그리고 사용되고 있는 메모리를 식별하는데, 이러한 과정을 Mark라고 한다. 이후에 Mark가 되지 않은 객체들을 메모리에서 제거하는데, 이러한 과정을 Sweep라고 한다.



#### Minor GC의 동작 방식
Minor GC를 정확히 이해하기 위해서는 Young 영역의 구조에 대해 이해를 해야 한다. Young 영역은 1개의 Eden 영역과 2개의 Survivor 영역, 총 3가지로 나뉘어진다.<br/>

- Eden 영역: 새로 생성된 객체가 할당(Allocation)되는 영역
- Survivor 영역: 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역

![](https://velog.velcdn.com/images/hllee/post/c0eb80d1-bdcf-4f34-9ab6-17789f1bc235/image.png)
객체가 새롭게 생성되면 Young 영역 중에서도 Eden 영역에 할당(Allocation)이 된다. 그리고 Eden 영역이 꽉 차면 Minor GC가 발생하게 되는데, 사용되지 않는 메모리는 해제되고 Eden 영역에 존재하는 객체는 (사용중인) Survivor 영역으로 옮겨지게 된다. Survivor 영역은 총 2개이지만 반드시 1개의 영역에만 데이터가 존재해야 한다.
<br/>
1. 새로 생성된 객체가 Eden 영역에 할당된다.<br/>
2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되고 Minor GC가 실행된다.
	2-1. Eden 영역에서 사용되지 않는 객체의 메모리가 해제된다.
	2-2. Eden 영역에서 살아남은 객체는 1개의 Survivor 영역으로 이동된다.<br/>
3. 1~2번의 과정이 반복되다가 Survivor 영역이 가득 차게 되면 Survivor 영역의 살아남은 객체를 다른 Survivor 영역으로 이동시킨다.(1개의 Survivor 영역은 반드시 빈 상태가 된다.)<br/>
4. 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동(Promotion)된다.<br/>
 
<br/>
객체의 생존 횟수를 카운트하기 위해 Minor GC에서 객체가 살아남은 횟수를 의미하는 age를 Object Header에 기록한다. 그리고 Minor GC 때 Object Header에 기록된 age를 보고 Promotion 여부를 결정한다.<br/>

또한 Survivor 영역 중 1개는 반드시 사용이 되어야 한다. 만약 두 Survivor 영역에 모두 데이터가 존재하거나, 모두 사용량이 0이라면 현재 시스템이 정상적인 상황이 아님을 파악할 수 있다.

<br/>

#### Major GC의 동작 방식
Young 영역에서 오래 살아남은 객체는 Old 영역으로 Promotion됨을 확인할 수 있었다. 그리고 Major GC는 객체들이 계속 Promotion되어 Old 영역의 메모리가 부족해지면 발생하게 된다. Young 영역은 일반적으로 Old 영역보다 크키가 작기 때문에 GC가 보통 0.5초에서 1초 사이에 끝난다. <br/>
그렇기 때문에 Minor GC는 애플리케이션에 크게 영향을 주지 않는다. 하지만 Old 영역은 Young 영역보다 크며 Young 영역을 참조할 수도 있다. 그렇기 때문에 Major GC는 일반적으로 Minor GC보다 10배 이상 시간이 오래걸린다.
 <br/>
 


### GC 동작 예제 코드
#### Java에서 가비지 컬렉션(GC) 동작을 보여주는 간단한 예제 코드
이 예제에서는 Person 클래스의 객체를 생성하고, 객체가 더 이상 참조되지 않을 때 GC가 동작하여 해당 객체를 수거한다.
```java
public class GarbageCollectionExample {

    public static void main(String[] args) {
        Person person1 = new Person("John");
        Person person2 = new Person("Alice");

        person1 = null; // person1의 참조를 제거
        System.gc(); // 가비지 컬렉션 요청

        // 다른 작업 수행
    }

    static class Person {
        private String name;

        public Person(String name) {
            this.name = name;
        }

        @Override
        protected void finalize() throws Throwable {
            System.out.println("Person object finalized: " + name);
        }
    }
}
```

위의 예제에서는 Person 클래스를 정의하고, Person 객체인 person1과 person2를 생성한다. 그런 다음 person1의 참조를 null로 설정하여 person1 객체를 더 이상 참조하지 않게 된다. 그리고 System.gc()를 호출하여 가비지 컬렉션을 요청한다.

Person 클래스에는 finalize() 메서드가 오버라이드되어 있다. 이 메서드는 객체가 GC에 의해 수거될 때 호출되는 메서드이다. 위의 예제에서는 finalize() 메서드가 호출될 때 "Person object finalized: "과 객체의 이름이 출력된다.

실행 결과로는 person1 객체가 GC에 의해 수거될 때 "Person object finalized: John"이 출력될 것이다. 이는 person1 객체가 더 이상 사용되지 않고 GC에 의해 수거되었음을 의미한다.

<br/>

### python 예제

**예제1) del로 객체 지워보기. **<br/>
아래와 같이 a라는 객체를 만들고 del(a)를 하면 객체가 메모리에서 지워짐과 동시에 delete라는 메시지를 출력하도록 하였다.<br/>

```python
class A:
    def __del__(self):
        print("deleted")

a = A()
del(a)
```
**결과**

> deleted

<br/>

**예제2) 오브젝트가 현재 참조중인 목록 알아내기

```python
import gc

def test():
    class A:
        pass

    class B:
        def __init__(self, obj): 
            self.obj = obj

    a = A()
    b = B(a)

    gc.collect() # make sure all garbage cleared before collecting referrers.    
    print( gc.get_referents(b))

test()
```

<br/>

--------------------------------

## 🔍메모리 누수(memory leak)란?
: CS 의미로 살펴볼 때, 컴퓨터 프로그램이 필요하지 않은 메모리를 계속 점유하고 있는 현상이다.<br/>

- 할당된 메모리를 사용한 다음 반환하지 않는 것이 누적되면 메모리가 낭비된다. 즉, 더 이상 불필요한 메모리가 해제되지 않으면서 메모리 할당을 잘못 관리할 때 발생한다.<br/>

- 일부 서적에서 메모리 손실이라는 용어로 뜻을 옮기기도 하지만 leak라는 표현은 단순히 잃는 것 이상의 개념이므로 누수라는 표현이 더 정확하다.<br/>

<br/>

#### 자바에서 메모리 누수(memory leak)
- 더 이상 사용되지 않는 객체들이 가비지 컬렉터에 의해 회수되지 않고 계속 누적이 되는 현상을 말한다.<br/>

- Old 영역에 계속 누적된 객체로 인해 Major GC가 빈번하게 발생하게 되면서, 프로그램 응답속도가 늦어지면서 성능 저하를 불러온다. 이는 결국 OutOfMemory Error로 프로그램이 종료되게 된다.<br/>
- 가비지 컬렉션을 통해 소멸 대상이 되는 객체가 되기 위해서는 어떠한 reference 변수에서 가르키지 않아야 한다.<br/>
- 다 쓴 객체에 대한 참조를 해제하지 않으면 가비지 컬렉션의 대상이 되지 않아 계속 메모리가 할당 되는 메모리 누수 현상이 발생 된다.

<br/>

### 메모리 누수(memory leak)가 발생하는 패턴

**1. Integer, Long 같은 래퍼 클래스(Wrapper)를 이용하여, 무의미한 객체를 생성하는 경우**
 
```java
public class Adder {
       publiclong addIncremental(long l)
       {
              Long sum=0L;
               sum =sum+l;
               return sum;
       }
       public static void main(String[] args) {
              Adder adder = new Adder();
              for(long ;i<1000;i++)
              {
                     adder.addIncremental(i);
              }
       }
}
```
long 대신 Long을 사용함으로써 , 오토 박싱으로 인해 sum=sum+l;에서 매 반복마다 새 개체를 생성하므로 1000개의 불필요한 객체가 생성된다.

<br/>
 
**2. 맵에 캐쉬 데이터를 선언하고 해제하지 않는 경우**
```java
import java.util.HashMap;
import java.util.Map;
public class Cache {
       private Map<String,String> map= new HashMap<String,String>();
       publicvoid initCache()
       {
              map.put("Anil", "Work as Engineer");
              map.put("Shamik", "Work as Java Engineer");
              map.put("Ram", "Work as Doctor");
       }
       public Map<String,String> getCache()
       {
              return map;
       }
       publicvoid forEachDisplay()
       {
              for(String key : map.keySet())
              {
                String val = map.get(key);                 
                System.out.println(key + " :: "+ val);
              }
       }
       public static void main(String[] args) {            
              Cache cache = new Cache();
              cache.initCache();
              cache.forEachDisplay();
       }
}
```
캐시에 직원과 직종을 넣었지만, 캐시를 지우지 않았다. 객체가 더 이상 사용되지 않을 때도 Map에 강력한 참조가 있기 때문에 GC가 되지 않는다.  

<br/>

**3. 스트림 객체를 사용하고 닫지 않는 경우**
```java
try
{
  Connection con = DriverManager.getConnection();
  …………………..
    con.close();
}

Catch(exception ex)
{
}
```
try 블록에서 연결 리소스를 닫으므로 예외가 발생하는 경우 연결이 닫히지 않는다. 따라서이 연결이 풀로 다시 돌아 오지 않기 때문에 메모리 누수가 발생한다. 또한 닫아지지 않아서 데드락이 발생할 가능 성이 크다.

<br/>

**4. 맵의 키를 사용자 객체로 정의하면서 equals(), hashcode()를 재정의 하지 않아서 같은 키로 착각하여 데이터가 계속 쌓이게 되는 경우**
```java
import java.util.HashMap;
import java.util.Map;

public class CustomKey {
       public CustomKey(String name)
       {
              this.name=name;
       }
       
       private String name;
       
       publicstaticvoid main(String[] args) {
              Map<CustomKey,String> map = new HashMap<CustomKey,String>();
              map.put(new CustomKey("Shamik"), "Shamik Mitra");
              String val = map.get(new CustomKey("Shamik"));
              System.out.println("Missing equals and hascode so value is not accessible from Map " + val);
       }
}
```
equals () 및 hashcode()를 재정의 해주지 않아서, 버킷에 계속 데이터가 쌓여 메모리 누수가 발생한다.

<br/>

**5. 맵의 키를 사용자 객체로 정의하면서 equals(), hashcode()를 재정의 하였지만, 키값이 불변(Immutable) 데이터가 아니라서 데이터 비교시 계속 변하게 되는 경우**
```java
import java.util.HashMap;
import java.util.Map;

public class MutableCustomKey {
    public MutableCustomKey(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + ((name == null) ? 0 : name.hashCode());
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        MutableCustomKey other = (MutableCustomKey) obj;
        if (name == null) {
            if (other.name != null)
                return false;
        } else if (!name.equals(other.name))
            return false;
        return true;
    }

    public static void main(String[] args) {
        MutableCustomKey key = new MutableCustomKey("Shamik");
        Map<MutableCustomKey, String> map = new HashMap<MutableCustomKey, String>();
        map.put(key, "Shamik Mitra");
        MutableCustomKey refKey = new MutableCustomKey("Shamik");
        String val = map.get(refKey);
        System.out.println("Value Found " + val);
        key.setName("Bubun");
        String val1 = map.get(refKey);
        System.out.println("Due to MutableKey value not found " + val1);
    }
```
속성이 변경되면 프로그램에선 찾을 수 없지만, Map에서는 참조가 있으므로 메모리 누수가 발생.

<br/>

**6. 자료구조를 생성하여 사용하면서, 구현 오류로 인해 메모리를 해제하지 않는 경우**
```java
public class Stack {

       privateint maxSize;
       privateint[] stackArray;
       privateint pointer;
       
       public Stack(int s) {
              maxSize = s;
              stackArray = newint[maxSize];
              pointer = -1;
       }
       
       public void push(int j) {
              stackArray[++pointer] = j;
       }
       
       public int pop() {
              return stackArray[pointer--];
       }
       
       public int peek() {
              return stackArray[pointer];
       }
       
       publicboolean isEmpty() {
              return (pointer == -1);
       }
       
       public boolean isFull() {
              return (pointer == maxSize - 1);
       }
       
       public static void main(String[] args) {
       
              Stack stack = new Stack(1000);
              
              for(int i = 0; i<1000; i++) {
                     stack.push(i);
              }
              
              for(int i = 0; i<1000; i++) {
                     int element = stack.pop();
                     System.out.println("Poped element is "+ element);
              }
       }
}
```

Stack이 1000으로 커지면서 내부 배열인 stackArray도 값이 채워지게 된다. 그 후 Stack를 pop하면 Stack의 참조된 공간이 비어있지만, stackArray에는 pop된 모든 참조가 포함되게 된다. 자바에선 이를 쓸모없는 참조라 불린다. 혹은 구식 참조(역 참조 할 수 없는 참조)라고 불린다. 배열에 해당 요소가 포함되어 있으면 GC가 될 수 없지만, pop된 후 에는 불필요하다.

<br/>

이 문제를 해결하려면 pop이 실행될 때, null 값을 설정하여 해당 객체가 GC가 되도록 해야한다. 
```java
public int pop() {
              int size = pointer--
              int element= stackArray[size];
              stackArray[size];
              return element;
       }
```

출처 - https://www.crocus.co.kr/1512, https://mangkyu.tistory.com/118, https://junghyungil.tistory.com/133
