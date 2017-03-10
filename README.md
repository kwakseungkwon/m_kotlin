# m_kotlin

## 문제1 - Simple Reactive System

간단한 Reactive 시스템을 구현해봅시다.

Reactive Programing은, 어떤 값이 변경되면, 그 값을 이용하고 있는 곳으로 상태가 전파됩니다.
잘 이해가 안되면, 엑셀의 수식을 생각하면 됩니다.
셀A와 셀B에 정수값이 있을 때, 셀C는 A+B라는 수식의 결과값이라고 가정해봅시다.
이 때, 셀A의 값을 변경하면, 셀 C의 값은 자동으로 변경됩니다.
그래도 잘 이해가 안가면, 이번 기회에 Reactive의 개념을 공부하세요.

https://en.wikipedia.org/wiki/Reactive_programming

알고리즘 문제가 아니고, 설계 문제입니다. 
알고리즘 문제를 푸는것 처럼 접근하면 안됩니다.

이번 문제의 입력과 출력은 아래와 같습니다.

### 입력
행이 하나밖에 없는 엑셀을 가정하면, 배열처럼 보일것입니다.
각각의 인덱스는 알파벳으로 이루어져있고, A~Z 까지입니다.

1. 이 배열은 **정수** 또는 **두개의 값으로 이루어진 사칙연산 수식**으로 이루어져 있습니다. (+ - * /)
2. 각각의 셀은 쉼표로 구분합니다.
3. 수식은 괄호로 묶여있고, 알파벨으로된 인덱스 또는 정수로 이루어집니다. 
4. 이 배열 다음에는 셀의 값이 어떻게 변경되는지가 순서대로 주어집니다.
5. 순환참조는 허용하지 않습니다. (A번째 인덱스의 수식에는 A가 포함되지 않는다는 뜻)
6. 값을 계산할 수 없다면(0으로 나누는 경우 등) #err을 출력합니다.

예를 들면, 아래와 같을 것입니다.

```text
100, 20, 3023, (A+E), 10, -30, (D+10), 0, 12345
A=>10
C=>(A*E)
E=>100
```

### 출력
셀의 값이 변경될 때 마다, 영향을 받는 셀의 변경값을 출력합니다.

```text
A=>10 : A=>10, D=>20, G=>30
C=>(A*E) : C=>100
E=>100 : E=>100, D=>110, G=>120, C=>1000
```

### 주의사항
* 기존의 Reactive 라이브러리(Rx등등)을 사용하면 반칙입니다.
* Reactive Programing의 모든 요소를 구현해낼 필요는 없고, Propagation(전파)만 제대로 되면 됩니다.

### kwi선임님을 위한 특별 문제
1. 입력과 출력을 안드로이드 GUI로 구현합니다.
2. Column이 하나뿐인 스프레드시트를 구현한다고 생각하면됩니다.
3. 세로 RecyclerView로 스크롤 가능하게 만들면 되겠죠? 거기에 선택된 셀의 값을 입력하는 EditText가 있으면 되겠네요.
4. Propagation은 셀에 직접 반영되면 됩니다.


## 문제2 - Simple Reactive System Extension
첫 문제인 Simple Reactive System을 통해 서로 비슷하게 구현된 부분이 있었습니다.
그것은 바로 **Cell**이라는 클래스였습니다.
**셀**에서 **표현식**을 파싱하여 이것이 숫자인지, 수식인지를 구분하여 값 계산에 활용하였습니다.
이제 수식 뿐만이 아니라 실제 엑셀 처럼 =Fun() 과 같이 여러 함수들을 제공하도록 확장한다고 가정해보죠.
지원되는 함수가 추가될 때마다 이전처럼 **isExpression**과 같이 특정 수식임을 구분하기위한 변수들을 계속 생성한다고 생각해보세요.
조건문이 난무하고 코드가 지저분해 보여 수정하고 싶어질 겁니다.
자 그럼 여기서 두번째 문제 입니다.

###Simple Reactive System 수식 확장
정수와 사칙연산만 제공하던 Simple Reactive System에 간단한 함수를 지원하도록 수식계산 부분을 확장합니다.
각 **수식 타입**에 알맞는 클래스들을 구현하여 위와 같이 지저분해질 부분들을 깔끔하게 정리해 봅시다. 

###입력 & 출력
* 기존 정수와 사칙연산에 대한 동작은 문제1과 동일합니다.
* 단, 수식 입력은 '='을 시작하는 것으로 변경합니다.(함수 입력과 동일하게 맞추도록 수정)
* 수식 입력대해 괄호 규칙도 편의상 제거하셔도 됩니다. =(A+E) 또는 =A+E 는 같은것으로 봄.
* 추가되는 함수에 대한 입력 형식은 다음과 같습니다.
* **=SUM(A:Z)**
함수의 시작은 '='로 시작하고 그뒤에 '함수명'과 '범위'가 입력됩니다.
* 지원할 함수는 SUM, AVERAGE, MAX, MIN 네가지 입니다.
범위내 값들중 비어있는 셀은 값을 무시하고 계산합니다.
(실제 엑셀처럼 AVERAGE의 경우 나누는 수에 카운팅을 포함하지 않음을 말합니다.)
```text
=SUM(A:Z) : A부터 Z셀 까지의 합을 구함
=AVERAGE(A:Z) : A부터 Z셀 까지의 평균을 구함
=MAX(A:Z) : A부터 Z셀 값중 가장 큰 값을 구함
=MIN(A:Z) : A부터 Z셀 값중 가장 작은 값을 구함
```
```
범위 제한 : A부터 Z셀 까지 입력 가능
(A:D), (C:Y), (E:F)와 같이 범위의 최소는 2셀이상이며 (A:A)와 같은 케이스는 지원되지 않음. 
(C:A)처럼 역으로 지정된 범위는 입력되지 않는다 가정
```

### 선택사항
문제1와 연결된 문제이므로, 문제1의 미완성에 따른 패널티가 존재합니다.
문제1을 마저 완성하고 구현해 보는것을 권장합니다만 상황을 고려하여 다음을 선택하실 수 있습니다.
가급적이면 문제1의 코드를 활용해 보는게 입출력 테스트 확인에 좋겠죠~?
* 풀었던 문제1 코드에 추가 구현
* 문제2에 해당되는 수식 확장에 대한 부분만 구현