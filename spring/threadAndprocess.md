 # Threa-safe

 # 1.Process vs. Thread
 ## Process
 컴퓨터에서 연속적으로 실행되고 있는 프로그램을 지칭한다. 프로세스는 메모리에 올라와 실행되고 있는 프로그램의 인스턴스로, 운영체제로부터 시스템 자원 ( CPU 시간, 운영되기 위해 필요한 주소 공간, [Code, Data, Stack, Heap] 구조로 되어 있는 독립된 메모리 영역 )을 할당 받는 작업의 단위이다.

## Process 특징
* 각각의 독립된 메모리 영역을 할당 받는다.
* 프로세스 1개당 최소 1개의 쓰레드를 갖는다.
* 프로세스들끼리는 통신으로 다른 프로세스의 자원에 접근해야 한다. 
    * IPC ( Inter-Process-communication )
    * ex: 파이프, 파일, 소켓 등
    * 왜? 프로세스는 각각의 독립된 메모리 영역을 할당 받기 때문에, 다른 프로세스의 자원에 직접적으로 접근할 수 없다.

## Multi-Process
하나의 응용 프로그램을 여러 개의 프로세스로 구성하여 각 프로세스가 하나의 작업을 처리한다.

### 장점
여러 개의 프로세스 중 하나에 문제가 발생하면, 그 프로세스만 죽는 걸로 끝이다. 피해가 다른 프로세스에게까지 가지 않는다.

### 단점
Context-switching 에서 오버헤드가 발생한다.
* 왜?
    캐시 메모리 초기화와 같이 무거운 작업으로 인해 많은 시간이 소모되기 때문이다.<br/>
IPC ( 프로세스 간의 통신 )를 하기 어렵다.

## Context-Switching
CPU에서 여러 프로세스가 돌아가면서 작업을 처리하는 과정을 말한다.<br/>
동작 중인 프로세스가 대기하면서 자신의 상태를 보관하고, 다른 대기하고 있던 다음 순서의 프로세스가 동작할 때, 이전에 보관했던 프로세스의 상태를 복구하는 작업을 말한다.<br/>
즉, 실행 중이던 프로세스가 대기 상태로, 대기 상태이던 프로세스가 실행 상태로 바뀌는 과정을 말한다.

## Thread
하나의 프로세스 내부에서 독립적으로 실행되는 하나의 작업 단위이다.

## Thread 특징
* 프로세스 내에서 각각 stack만 따로 할당 받고, [code, Data, heap] 메모리 영역은 공유한다.
* 같은 프로세스 내의 쓰레드들은 프로세스의 주소 공간이나 자원을 공유한다.
* 한 쓰레드가 프로세스 자원을 변경하면, 다른 이웃 쓰레드도 그 변경 결과를 즉시 확인할 수 있다.

## Multi-Thread
하나의 프로세스를 다수의 실행 단위로 구분하여 쓰레드들이 프로세스의 자원을 공유한다.

### 사용 이유
1. 프로세스를 이용하여 동시에 처리하던 작업을 쓰레드로 구현하면, 메모리 공간과 시스템 자원 소모가 줄어들게 된다.
2. 쓰레드 간의 통신이 필요한 경우, 별도의 자원을 이용하는 것이 아니라 전역변수의 공간 혹은 동적으로 할당된 공간인 Heap 영역을 이용하여 서로 데이터를 주고 받을 수 있다.
3. 쓰레드의 context-switching은 프로세스의 context-switching과 달리 캐시 메모리 초기화를 하지 않아서 속도가 더 빠르다.
4. 시스템이 처리해야 할 작업이 줄고, 자원소모가 줄어 들어 결과적으로 프로그램의 응답시간이 단축된다.

### 단점
1. 동일한 자원에 동시에 접근하는 일에 대해 신경 써야 한다.
2. 서로 다른 쓰레드가 메모리 영역을 공유하기 때문에 다른 쓰레드에서 사용 중인 데이터를 읽어오거나 수정할 수 있다.
3. 동기화 작업이 필요하다.
    * 작업 처리 순서를 컨트롤하고 공유 자원에 대한 접근을 제한해야 한다.
4. 병목현상이 발생하여 성능 저하 가능성이 있다.
    * 과도한 lock으로 인한 병목현상을 줄여야 한다.
5. 공유 자원이 아닌 부분은 동기화를 할 필요가 없다.

#### 병목현상
어떤 시스템 내 데이터의 집중적( 과도한 )사용으로 인해 전체 시스템에 절대적 영향을 미치는 부분의 사용 빈도가 늘어나, 그 부분의 성능이 저하되면서 전체 시스템이 마비되는 현상을 말한다.

# 2.Thread-safe
multi-thread 프로그래밍에서 일반적으로 어떤 함수나 변수 혹은 객체가 여러 쓰레드로부터 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없는 상태를 말한다.<br/>
여러 쓰레드가 같은 함수를 동시에 실행할 경우, 함수가 이용하는 쓰레드 간 공유 자원이 문제다. 공유 자원을 lock 같은 동기화 방식으로 보호하여 공유 자원의 무결성을 보장해야 한다. 이렇게 공유 자원의 무결성을 보장하는 함수를 Thread-safe 함수라고 한다.

## How to Thread-safe 
1. Re-entrancy
어떤 함수가 한 쓰레드에 의해 호출되어 실행 중이다. 이때, 다른 쓰레드가 그 함수를 호출하더라도 그 결과가 각각 올바르게 나와야 한다.<br/>
여러 쓰레드에서 같은 함수를 동시에 실행할 수 있지만, 쓰레드간의 공유 자원을 이요하지 않는 것을 의미한다. 즉, 공유 변수를 사용하지 않기 때문에 각 쓰레드는 언제나 같은 호출 결과를 얻을 수 있다.

![Untitled](http://ssup2.tistory.com/entry/Threadsafe-%ED%95%A8%EC%88%98-Reentrant-%ED%95%A8%EC%88%98)

   > thread-safe 함수는 함수 내부에서 전역변수를 사용하기 때문에 race condition을 방지해야 한다. re-entrancy 함수는 함수 내부에서 지역 변수만을 사용하기 때문에 언제 호출하더라도 항상 일정한 결과를 낸다.

2. Thread-local storage
데이터를 하나의 쓰레드에서만 접근할 수 있도록 하여 여러 쓰레드로부터 데이터가 변경될 가능성을 줄인다. 예를 들면, 지역변수는 stack 영역에 저장되기 때문에, 특정 함수가 여러 번 호출되더라도 그 호출에는 고유한 지역변수를 가지고 있게 된다. 이는 앞서 re-entrancy 설명과 이어진다.

3. Mutual Exclusion
공유 자원을 반드시 사용해야 하는 경우, 자원의 접근을 세마포어로 통제한다.

4. Automic operation
데이터를 변경할 경우, automic하게 데이터에 접근하도록 만든다.
<br/>
원자와 같이 분할할 수 없다는 것을 비유한 표현으로, multi-thread 프로그램에서 공유 자원들에 대해 여러 쓰레드가 동시에 접근하는 경쟁상태(race condition)을 막기 위한 방법이다.
이것이 가능하려면 아래 2가지 조건을 만족해야 한다. <br/>
    a. 모든 조작이 완료될 때까지 어떤 프로세스도 변경을 알지 못하도록 비가시적이어야 한다.
    b. 조작 중에 어느 하나라도 실패한다면 조작 전체도 실패하고 시스템의 상태를 조작 이전으로 되돌려야 한다.
    <br/>

어떤 프로세스가 조작을 하고 있다고 가정하자. 그러면 다른 프로세스 즉, 외부에서는 조작의 성공과 실패만을 확인할 수 있다. 그 과정을 알지 못한다. 이것을 automic operation이라고 한다.

![Untitled](https://vaert.tistory.com/39)

5. Immutable Object
객체를 생성한 이후에 값을 변경할 수 없도록 만든다.
예를 들면, 자바에서는 private final을 사용한다.

## 세마포어 vs. 뮤텍스

* 세마포어
공유 자원의 데이터 혹은 critical section (임계영역)에 n개의 프로세스 혹은 쓰레드가 접근하는 것을 막아준다. <br/>
자원의 상태를 나타내는 카운터로 볼 수 있다. <br/>
비교적 긴 시간을 확보하는 자원에 대해 세마포어를 사용한다.<br/>
운영체제 또는 커널의 지정된 저장장치 내부의 값으로서, 각 프로세스는 이를 확인하고 변경할 수 있다. 확인하는 값에 따라 프로세스가 즉시 자원을 사용할 수 있거나 이미 다른 프로세스에 의해 사용 중이라는 점을 알게 되면 재시도하기 전까지 일정 시간을 기다려야 한다. 이러한 상태를 나타내기 위해 세마포어는 이진수를 사용한다 ( 혹은 추가적인 값을 가질 수도 있다.) 

* 뮤텍스
공유 자원의 데이터 혹은 cirtical section (임계영역)에 1개의 프로세스 혹은 쓰레드가 접근하는 것을 막아준다.<br/>
critical section을 가진 쓰레드들의 running time이 서로 겹치지 않게 각각 단독으로 실행되게 하는 기술이다. 즉, 2개의 쓰레드가 뮤텍스 객체를 동시에 사용할 수 없다. <br/>

=>> 세마포어는 뮤텍스가 될 수 있다. 하지만! 뮤텍스는 세마포어가 될 수 없다.<br/>

* critical section (임계영역)
여러 프로세스가 데이터를 공유하며 작업을 할 때, 각 프로세스에서 공유 데이터에 접근하는 프로그램의 코드 부분을 가리킨다.

![Untitled](https://jwprogramming.tistory.com/13)