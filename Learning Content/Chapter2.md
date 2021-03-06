## 1. 플로우 기초
~~~
- Flow는 코틀린에서 쓸 수 있는 비동기 스트림
- .collect와 함께써야한다 
- flow는 콜드 스트림이다.(콜트스트림은 요청이 있을때만 값이 전달된다.)
- withTimeoutOrNull방식으로 flow를 취소할 수 있다.
- flow이외에도 flowOf, asFlow등의 플로우 빌더가 있다.
- flowOf는 여러 값을 인자로 전달해 플로우를 만듭니다.
- asFlow는 컬렉션이나 시퀀스를 전달해 플로우를 만들 수 있다.
~~~

## 2. 플로우 연산
~~~
- 플로우에서 map 연산을 통해 데이터를 가공할 수 있습니다.
- filter는 조건에 맞는 요소만 남기는것 필터를 이용해 플로우의 데이터를 가공 할수 있다.
- filterNot을 통해 반대의 값을 얻을 수도 있다.
- transform 연사자를 이용해 조금 더 유연하게 스트림을 변형할 수 있다.
- take연산자는 넣은 값의 개수 만큼의 결과만을 출력한다.
- takeWhile은 take를 이용해서 갯수가 아니라 조건에 맞는것 만을 가지고 용도이다.
- take와 반대역할을 하는 drop이 있다
- drop은 처음 몇개의 결과를 버린다.(dropWhile도 존재한다.)
- reduce는 흔히 map과 reduce로 함께 소개되는 함수형 언어의 오래된 메커니즘이다.
  첫번쨰 값을 결과에 넣은 후 가 값을 가져와 누진적으로 계산합니다.
- 두개의 값을 가지고 와서 가공한후 값을 A에넣고 다음값을 다시B에 넣는 방식
- fold 연산자는 초기값이 있다는 차이만 있습니다.
- count는 종단 연산자이므로 특정값, 결과를 나타내지만
  filter의 중간연산자이기 때문에 .collect를 써야 결과를 볼수 있다.
~~~

## 3. 플로우 컨텍스트
~~~
- 플로우 내에서는 컨텍스트를 바꿀 수 없다
- flowOn은 업스트림과 다운 스트림으로 나눠진다. 업스트림만 flowOn에 있는 컨텍스트로 동작한다
- 실수로 두개를 붙이게되면 default만 인정이 된다.
~~~

## 4. 플로우 버퍼링
~~~
- 버퍼를 사용하면 flow측에 버퍼를 붙이면 collect쪽에 준비와는 상관없이 데이터를 전송한다.
  때문에 지연시간이 줄어든다.
- conflate는 만약 처리보다 빨리 발생한 데이터가 있으면 중간 값들을 누락합니다.
- conflate는 내가처리하고 있는 시간의 중간 값들을 다 버리는것
- collectLatest는 첫번째 값을 받고 실행중에 두번째값이 오면 리셋을 하고 두번째값을 처리한다.
~~~

## 5. 플로우 결합하기
~~~
- zip은 양쪽의 데이터를 한꺼번에 묶는 새로운 데이터를 만들어 냅니다.
- combine은 양쪽의 데이터를 같은 시점에 묶지 않고 한쪽이 갱신되면 새로 묶어 데이터를 만듭니다.
- 용도에 따라서 완전히 짝을 맞추어야 할때는 zip을 써야하고 가장 최신의 데이터가 필요하다라고 생각될때 combin을 사용한다.
~~~

## 6. 플로우 플래트닝하기
~~~
- flatMap은 3가지를 지원하고 있는데 flatMapConcat, flatMapMerge, flatMapLastest 이다
- flatMapConcat은 결과를 이어붙이겠다고 보면된다. (결과 -> 1-1,2-1,1-2,2-2)
- flatMapMerge 진행중이어도 계속 보내면서 합쳐버린다.(결과 -> 1-1,1-2,2-1,2-2)
- flatMapLastest 이전에 호출이 진행중이라면 캔슬시키고 다음으로 바로 넘어간다.(delay가 길다면 뒤는 동작하지않는다. / 결과-> 1-1,2-1,2-2)
~~~

## 7. 플로우 예외처리하기
~~~
- 예외는 collect을 하는 수집기 측에서도 try-catch 식을 이용해 할 수 있습니다.
- 빌더 코드 블록 내에서 예외를 처리하는 것은 예외 투명성을 어기는 것입니다.
  플로우에서는 catch 연산자를 이용하는 것을 권합니다.
- catch 블록에서 예외를 새로운 데이터로 만들어 emit을 하거나, 다시 예외를 던지거나, 로그를 남길 수 있습니다.
~~~

## 8. 플로우 완료처리 하기
~~~
- 완료를 처리하는 방법 중의 하나는 명령형의 방식으로 finally 블록을 사용한다.
- 선어적으로 완료처리 하기위해서는 onCompletion을 사용하여 완료를 처리할 수 있다.
- 다른 장점으로는 cause를 사용하여 에러가 발생할경우 처리할 수 있다.
- try catch는 catch에서는 문제를 알수 있지만 finally에서의 문제는 알 수 없다.
  이점이 가장 큰 onCompletion의 장점이라고 할 수 있다.
~~~

## 9. 플로우 런칭
~~~
- addEventLinstener 대신 플로우의 onEach를 사용할 수 있습니다. 이벤트 마다 onEach가 대응하는 것입니다.
- 한가지 문제점이 존재한다. 이벤트를 collect로 받으면 이벤트는 끝나지않기 때문에 다른게 동작하지 않을수 있다.
- launchIn을 이용하면 별도의 코루틴에서 플로우를 런칭할 수 있다.
- launchIn은 첫번째인자로 코루틴 스코프이다. 새로운 코루틴을 만드는것이 떄문에 다른 동작이 가능하다.
- 다른 작업이 존재할 때에는 launchIn을 통해서 수행하는것이 효율적이다.
~~~






