# Combine

### Combine

![image](https://user-images.githubusercontent.com/77050826/161272833-caed7e80-6c2a-4993-97da-503000535e4a.png)</br>
   - @State는 String, Int, Bool과 같은 값은 간단한 값을 저장 -> View에 현재 상태를 표시할 때 사용</br>
![image](https://user-images.githubusercontent.com/77050826/161273049-bedf10e8-aa49-458d-b4e6-b60c23bc9f5b.png)</br>
   - @Bindingdms childView에서 ParentView의 값을 표시하고 능동적으로 값이 변화할 때 childView에서 사용함</br>
   
   - combine에는 두가지 기초 protocol
      - Publisher: 관심 있는 누구에게나 value를 전달할 수 있는 타입</br>
      - Subscriber: value를 받을 수 있는 타입</br>
      - <img width="619" alt="image" src="https://user-images.githubusercontent.com/77050826/161298641-88afa9ee-ec8f-4056-b50c-f04c0ab3ec8b.png"></br>
      
   - subscriber 프로토콜에 포함된 세가지</br>
      - receiveSubscription: Publisher와 subscriber가 연결된 순간이다. subscriber객체를 사용하여 publisher에게 원하는 값을 요구할 수 있는 단계이다. </br>
      - reciveValue: value 값을 받아 액션을 취해줄 수 있다. Publisher에게 더 많은 value를 원하는지 알릴 수 있다.</br>
      - receiveCompletion: publisher가 완료되는 단계로 완료되는 순간 완료되었다고 전달한다. (전달 완료 or 실패로 나타낸다)</br>
      - code 구현 예제</br>
         - <img width="481" alt="image" src="https://user-images.githubusercontent.com/77050826/161299884-56b19497-5659-4df3-8bb9-3e2eccb4f257.png"></br>
      - **그러나 이러한 모든 과정을 거치지 않고 sink를 사용하여 간단히 subscriber를 구현할 수 있다.**</br>
         - <img width="482" alt="image" src="https://user-images.githubusercontent.com/77050826/161300659-3fcf770c-c869-434e-a0fa-93cbffea0714.png"></br>
            - canceelable.cancle()를 사용하여 subscriber에 저장되지 않도록 할 수도 있다.</br>
            - cancellable을 해주는 것을 잊지 말자!</br>

   - combine의 subject protocol
      - CurrentValueSubject</br>
         - single 값을 래핑 -> 변경될 때마다 새로운 element를 publish 해준다.</br>
         - 가장 최근에 publishehls element의 버퍼를 유지해줌</br>
         - send를 호출하면 현재의 값도 직접 업데이트됨</br>
      - PassThroughSubject</br>
         - downstream subscirbers에게 element를 broadcasts하는 subject</br>
         - CurrentValue와의 차이점으론 가장 최근에 publish된 초기값 or 버퍼가 없음
         - subscribers가 없으면 value를 삭제한다.

   - combine에 있는 scheduler, cancellable
      - cancellable: combine의 기본 프로토콜로 뜻 그대로 모든 것을 cancell 시킨다고 볼 수 있다.(할당된 모든 리소스가 해제된다.</br>
   - ***Combine Operator***
      - map: upstream publisher의 모든 요소들을 반환해줌</br>
      - tryMap: map과 같은 원리이지만 error를 보여줄 수 있다.</br>
      <img width="385" alt="image" src="https://user-images.githubusercontent.com/77050826/161458274-9ffa545a-ea43-4ad5-8868-2303d2693654.png"></br>
      - flatMap: map, tryMap 괴는 다르게 publisher를 반환해주는 operator
         - 기본값은 unlimited이다.</br>
         - maxPublisher를 max(1)로 설정한다는 것 -> publish한번 당 최대 1개의 publisher만 만들어 내겠다는 뜻.</br>
         - 모든 publisher가 성공적으로 완료 돼도 전체 스트림이 완료되지 않음.</br>
         - But 새로운 publisher를 생성 실패시 전체 스트림이 실패함.</br>
         - maxPublisher의 갯수만큼 실행돼고 계속 스트림이 살아있음.</br>
         - But 메모리적 문제가 발생할 수 있음.</br>
      - mapError: upStream Publisher의 오류들을 전부 새로운 오류로 변환해줌.</br>
      - scan: 예제코드</br>
      <img width="453" alt="image" src="https://user-images.githubusercontent.com/77050826/161459578-766c98c9-7029-4864-ace2-612ef4531fbf.png"></br>
      - tryScan: map, trymap 사이의 관계와 scan과 똑같다.</br>
         - scna 도중 문제가 생기면 error을 반환한다.</br>
      - filter: 말 그대로 필터로 걸로 조건에 맞는 요소들을 다시 publish해주는 것이다.</br>
         - .filter{ $0 % 2 == 1 }을 추가시 홀수들만 publish되는 것이다.</br>
      - tryFilter: 위와 같은 맥락이다. 그래도 다시 얘기하자면 try가 붙으면 error값을 보여주는 closure를 넣어줄 수 있다는 것이다.</br>
      - compactMap: pusblisher 스트림에 있는 nil을 제거한후 나머지만 다운스트림 해준다.</br>
         - 배열 [1,2,3,4,nill]이 있다고 가정하면 1,2,3,4만 남겨주는 것이다</br>
