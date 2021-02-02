---
title: kapt InvocationTargetException
layout: single
categories:
  - android
tags:
  - android
  - kapt
---
Annotation Processor를 이용한 라이브러리를 사용하다 보면 뭔가 실수했을 때
`kaptDebugKotlin InvocationTargetException`이라는 에러를 종종 마주한다. 어디서 틀렸는지는
정확히 안 알려주고 저렇게만 나와서 모르고 만났을 땐 심히 당황스럽다.

이럴 때는 그냥 build를 돌리면 정확한 로그를 잘 안 보여줘서
`gradlew :app:kaptDebugKotlin --stacktrace` 처럼 --stacktrace를 붙이면 뭐가 문젠지 잘 알려준다.

![stacktrace](/assets/images/post/2021-02-02/stacktrace.png)

RealmObject 안에 final field를 넣어서 에러 나는 경우를 캡처 해봤다.
