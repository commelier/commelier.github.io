---
layout: post
title: Study RxJava - 1
excerpt : "RxJava 개인 공부용 포스팅입니다."
date:   2019-06-01
tags: [study, rxjava]
comments: false
---
*개인적으로 공부하면서 정리한 내용입니다. 모든 내용을 포함하지 않고 1차로 전체적인 내용 공부를 먼저 하고, 2차로 다시 정리한 내용이기 때문에 누락된 개념들이 많습니다.*
<br><br><br>
1. RxJava를 만들게 된 이유
    - Embrace Concurrency → 서비스 계층에서 동시성을 취함
    - Java Futures are Expensive to Compose → 그 당시 자바8에서 제공하는 CompleteFuture 같은 클래스가 제공되지 않았음. RxJava에서 비동기 흐름을 조합할 수 있음.
    - Callbacks have their Own Problems → 콜백 지옥이 코드의 가독성을 떨어뜨림
<br><br>
2. Observable 클래스 - 데이터의 변화가 발생하는 data source, 옵저버 패턴을 구현함
    - onNext : Observable이 데이터 발행을 알림
    - onComplete : 모든 데이터의 발행을 완료했을 때. onComplete 이후 onNext 이벤트 발생하면 안 됨.
    - onError : 에러 발생 이벤트. 이 이벤트가 발생하면 이후 onNext와 onComplete 이벤트가 발생하지 않음.<br>
    
- just() : Observable을 생성
- subscribe() : 실제 데이터를 발행하는 함수
- create() : 위 세 가지 이벤트를 개발자가 직접 호출해줘야 함.
- fromXXX() : 단일 데이터가 아닐 때 사용하는 함수 종류들
- fromArray() : 배열이 들어있는 데이터를 처리할 때 사용.
- fromIterable() : Iterable 인터페이스를 구현한 클래스에서 Observable 객체를 생성. Iterator를 반환함. 데이터 타입에 의존하지 않는 장점.
- fromCallable() : Callable 인터페이스의 call() 메서드를 구현한 후 인자로 담아서 RxJava와 연동
- fromFuture() :  비동기 계산의 결과를 구할 때 사용. Executor 인터페이스를 구현한 클래스에 Callable 객체를 인자로 넣어 Future 객체를 반환. get()메서드를 호출하면 Callable 객체에서 구현한 계산 결과가 나올 때까지 블로킹 됨.
- fromPublisher() : 자바9 표준 API 일부. (확인 필요)
