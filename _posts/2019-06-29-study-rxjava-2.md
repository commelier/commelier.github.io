---
layout: post
title: Study RxJava - 2
excerpt : "RxJava 개인 공부용 포스팅입니다."
date:   2019-06-29
tags: [study, rxjava]
comments: false
---
*개인적으로 공부하면서 정리한 내용입니다. 모든 내용을 포함하지 않고 1차로 전체적인 내용 공부를 먼저 하고, 2차로 다시 정리한 내용이기 때문에 누락된 개념들이 많습니다.*
<br><br>

- Single 클래스
    - Observable 클래스는 데이터를 무한하게 발행할 수 있지만, Single 클래스는 오직 1개의 데이터만 발행하도록 한정. 데이터 하나가 발행됨과 동시에 종료. onNext()와 onComplete()가 onSuccess()로 통합된 것
- Maybe 클래스
    - Single 클래스와 마찬가지로 최대 데이터를 하나 가질 수 있지만, 데이터 발행 없이 바로 데이터 발생을 완료할 수 있음.
- Hot Observable
    - Cold Observable은 subscribe() 함수를 호출하여 구독하지 않으면 데이터 발행 X. 구독하면 준비된 데이터를 처음부터 발행.
    - Hot Observable은 구독자가 존재 여부와 관계 없이 데이터를 발행하는 Observable (여러 구독자를 고려할 수 있으나 데이터를 처음부터 모두 수신할 것을 보장할 수 없음.). 구독한 시점부터 발행.
    - Hot Observable은 배압(Back Pressure)을 고려해야 한다.
    - Back Pressure → 데이터를 발행하는 속도와 구독자가 처리하는 속도의 차이가 클 때 발생. (Flowable 클래스에서 배압을 처리함)
    - Cold Observable → Hot Observable 하기 위해서는 Subject 객체를 만들거나 ConnectableObservable 클래스를 활용한다.

- Subject 클래스
    - Observable의 속성과 구독자의 속성이 모두 있음. Observable처럼 데이터를 발행할 수도 있고, 구독자처럼 발행된 데이터를 바로 처리할 수도 있음.
- AsyncSubject 클래스
    - Observable에서 발행한 마지막 데이터를 얻어올 수 있는 Subject 클래스. 완료되기 이전까지는 구독자에게 데이터를 전달하지 않다가 완료되면 구독자들에게 마지막 데이터를 발행하고 종료한다.
- BehaviorSubject 클래스
    - 구독자가 구독을 하면, 가장 최근 값 혹은 기본 값을 넘겨주는 클래스. createDefault()로 생성. 구독자가 subscribe()함수를 호출했을 때 그 전까지 발행한 값이 없다면 기본값을 대신 발행해야 함.
- PublishSubject클래스
    - 구독자가 subscribe() 함수를 호출하면 값을 발행하기 시작한다. 해당 시간에 발행한 데이터만 그대로 구독자에게 전달 받는다.
- ReplaySubject 클래스
    - Subject 클래스의 목적은 Hot Observable을 활용하는 것인데, Cold Observable처럼 동작하기 때문에 주의해야 한다.
    - 구독자가 새로 생기면 데이터를 처음부터 끝까지 발행하는 것을 보장.

    → 모든 데이터의 내용을 저장하는 과정 중 메모리 누수가 발생할 가능성도 염두해둬야 함.

- ConnectableObservable 클래스
    - Cold Observable → Hot Observable
    - Observable을 여러 구독자에게 공유할 수 있으므로, 원 데이터 하나를 여러 구독자에게 동시에 전달할 때 사용한다.
    - connect()함수 호출 이후에 데이터 발생
