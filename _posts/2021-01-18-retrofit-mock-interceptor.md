---
title: Retrofit Mock Interceptor
layout: single
categories:
  - android
tags:
  - android
  - retrofit
---
작업을 하다 보면 일정이 항상 맞물리지 않다 보니 API는 나오지 않았는데 UI만 먼저 그려야 하는
경우도 종종 있다.

Repository의 Interface만 따로 mock 구현체를 만들어도 되지만 API 형태에
맞게 Data Access Object를 맞추는 작업은 서버 구현이 완료되고 난 후까지 밀리게 된다.

그러다 API와 UI에 갭이 커서 안 맞는 부분이라도 생기면 서버 개발자분께 다시 수정을 요청드리고
하염없이 기다리는 시간을 갖게 된다.

### 힘들다!

차라리 내가 야근해서 해결되는 일이면 야근하고 말겠는데 손댈 수 없는 상황 때문에 일정이
밀리는 걸 보고 있자면 정말 피 말리고 괴롭다.

이런 상황을 방지하기 위해 API의 JSON 형태를 미리 받아두면 그 데이터로 UI를 그려보면서
필요하지 않은 데 있거나, 필요한 데 없거나, 이대로 그릴 수는 있지만 쌍방이 더 힘들어지는 구조를
미리 파악하고 일정이 꼬이는 걸 방지할 수 있다.

여기까지가 이번 포스팅의 기본전제다. 서버에서 API 형태는 받았지만, 실제 데이터는 없는 상황.

### 이거 가지고 어떻게 테스트하지?

여러가지 방법이 있겠지만 여기서는 Retrofit의 Interceptor를 이용하는 방식을 소개한다.
모든 걸 만들어놓고 API Interface에서 필드 하나만 넣었다 빼면 끝이다.

일단 Interceptor의 코드는 이렇게 생겼다. 당연히 Debug모드일 때만 추가해줘야 한다.

{% gist zmunm/8c394962b56ccb0badecb4f5a98419c5 MockInterceptor.kt %}

별거 없다. request에서 `mock`이랑 `mock_code`라는 쿼리를 찾아서 그걸 response로 부메랑처럼 돌려준다.

API Interface에서 아래랑 비슷하게 쿼리를 추가해주면 된다.

{% gist zmunm/8c394962b56ccb0badecb4f5a98419c5 SampleApi.kt %}

### 마치며

잘 짜여진 API를 보고 깔끔하게 돌아가는 앱을 만드는 것도 좋지만, 이리저리 꼬여있는 상황에 유연하게
대처하는 방법을 배울 수 있는 게 실무의 묘미 같다.
