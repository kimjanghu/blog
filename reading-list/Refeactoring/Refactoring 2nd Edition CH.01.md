# Refactoring 2nd Edition CH.01

### TL;DR

> 수정이 쉬운 코드를 작성하자. 결국 좋은 코드는 수정하기 쉬운 코드다.

* Split Loop to isolate the accumulation (반복문 분리)
* Slide Statements to bring the initializing code next to the accumulation (변수 위치 조정)
* Extract Function to create a function for calculating the total (계산을 별도 함수로 추출)
* Inline Variable to remove the variable completely (변수 인라인 적용)



Chapter 01에서는 특정 예시를 통해 rough하게 리팩토링하는 과정을 소개하고 감을 익히는 정도의 내용이 적혀있다.

### 리팩토링이 필요한 이유?

코드의 유지보수는 사람이 한다. 프로그램이 잘 동작 하더라도, 사람이 이해하지 못한다면 유지보수에 많은 시간과 노력이 할애된다. 따라서 이를 용이하게 하기 위해 코드를 깔끔하게 작성하는 것은 중요하다. 복잡도가 낮고 새로운 요구사항에 대응할 수 있는 구조로 코드를 리팩토링 한 후에 기능을 추가하자. 

이 과정에서 테스트 코드를 작성하여, 리팩토링 과정에서 테스트를 통해 결과에 대한 신뢰성을 유지하는 것은 중요하다. 간단한 수정이라도 리팩토링 진행 후에는 테스트 진행하는 습관을 갖고, 작은 단위로 코드를 리팩토링하여 실수를 줄여야 한다. 

또한 코드를 작성할 때 사람이 이해하기 쉬운 변수명을 사용하자. 이름이 좋으면 함수를 읽지 않고도 코드를 이해할 수 있다. 

>Any fool can write code that a computer can understand. Good programmers write code that humans can understand



### 방법 1. 함수 추출

코드를 각각의 부분으로 나눌 수 있는 지점을 찾고 추출이 용이하도록 코드를 작성한다. 예를 들어 분기 처리 부분과 반복문은 함수로 추출하기 좋다. 여기서 주의해야할 점은 함수로 추출했을 때, 해당 부분에 있는 변수를 곧바로 사용 가능한지 불가능한지 확인 후 진행하자.



### 방법 2. 변수 인라인

임시 변수를 질의 함수로 바꾼다. 예를들어 아래와 같이 대입문의 우변을 함수로 추출한다.

```javascript
const play = plays[perf.playID];

function playFor(perf) {
	return plays[perf.playID]
}

const play = playFor(perf);
```

이후, 할당된 변수를 inline으로 작성한다. 지역 변수를 제거하면 변수의 유효범위를 신경써야 할 대상이 줄어들기 때문에 함수 추출이 용이해진다.

```javascript
const play = playFor(perf);
let thisAmount = anyFunction(play);
```

```javascript
let thisAmount = anyFunction(playFor(perf));
```

변수를 인라인 하게되면, 특정 함수가 반복적으로 사용 되어 성능이 저하될 수 있으나, 제대로 리팩토링된 코드베이스는 성능 개선하기 용이하다.



### 방법 3. 변수 위치 조정

반복문 내 기능 구현 코드가 여러 개라면 분리 후 작성하는 것이 좋다.

```javascript
let someThingAVariable = '';
let someThingBVariable = '';

for () {
	someThingA

	someThingB
}
```

```javascript
let someThingAVariable = '';

for () {
	someThingA
}

let someThingBVariable = '';

for () {
	someThingB
}
```

반복문 내에서 사용하는 변수를 해당 로직 앞으로 옮긴다. 즉, 변수 선언하거나 초기화하는 부분을 관련 코드 앞쪽으로 수정하면 함수 분리가 용이해진다.

이러한 과정은 논리적인 요소를 파악하기 쉽도록 코드의 구조를 변경하는 작업이다. 이 작업이 선행되어야 이후 개선 작업이 쉬워진다.



### 기능 추가 및 변경

구조를 변경하는 작업이 끝났다면, 코드의 파악이 용이해진다. 자연스럽게 특정 함수의 기능을 추가하기 위한 구조 파악도 쉬워진다.

기능을 추가하기 위해 함수의 단계를 분리할 수 있는지 파악 후 추출하는 과정을 거친다. 예를 들어 데이터를 처리하는 부분과 그 데이터를 받아서 HTML 등을 Rendering 하는 부분 등으로 나눈다.

이러한 경우에도 함수를 추출하여, 각 기능을 구분한 뒤 모듈화한다. 리팩토링의 기본적인 원리는 동일하며, 명료하게 각 기능을 모듈화 할 수록 전반적인 로직을 파악하기 쉬워진다.



### 조건부 로직을 Polymorphism 으로 바꾸기

(리팩토링 책 기준 예시 기준) 각 공연 장르별 공연료와 적립 포인트 계산법을 다르게 가져가고 싶은 경우에는 확장 가능한 class 를 생성 후 재사용한다. 조건문으로 작성되어있는 계산식을 Polymorphism 으로 변경하고, 각 공연료와 적립 포인트 계산을 담을 수 있는 sub class를 생성한다면 이후에 장르가 추가되어도 쉽게 기능 추가가 가능해진다.



### 정리

코드는 명확해야한다. 유지보수 과정에서 로직을 쉽게 파악하고, 수정할 수 있어야한다. 건강한 코드 구조는 생산성을 극대화하고, 기능을 쉽게 추가할 수 있기 때문에 고객에게 필요한 기능을 빠르고 저렴한 비용으로 제공하도록 해준다.

리팩토링 과정의 핵심은 단계를 잘게 나눠야 더 빠르게 처리할 수 있고, 코드에 대한 결과를 그대로 유지하며, 이러한 작은 단계가 모여 큰 변화를 이룬다는 것에 있다.




