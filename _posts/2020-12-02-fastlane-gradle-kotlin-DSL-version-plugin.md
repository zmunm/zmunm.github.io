---
title: fastlane과 gradle kotlin DSL 함께 사용하기
layout: single
categories:
  - ruby
tags:
  - ruby
  - fastlane
  - android
  - gradle
  - gradle-kotlin-dsl
---
android에 fastlane을 적용하려면 가뜩이나 자료가 많지 않다. 그래도 어떻게든 해냈었다.
그리고 한참 뒤 gradle을 kotlin dsl 로 마이그레이션 해봤다.


# 문제가 생겼다!

원래는 [android_versioning](https://github.com/otkmnb2783/fastlane-plugin-android_versioning)
이라는 플러그인으로 gradle에서 app version name과 version code를 가져오고 있었다.
그런데 gradle kotlin dsl로 마이그레이션하고 나니 이 값들을 못 가져왔다.

# 왜 그런가 플러그인을 뜯어보니..
gradle 파일에서 아래 정규식에 해당하는 값을 읽어오는 구조였다.
```ruby
Regexp.new(/\s*(?<key>#{params[:key]}\s*)(?<left>[\'\"]?)(?<value>[a-zA-Z0-9\.\_]*)(?<right>[\'\"]?)(?<comment>.*)/)
```

기존 build.gradle에서는

```groovy
versionName '1.0.0'
versionCode 1
```

모든 걸 띄어쓰기로 해결해서 저 정규식 하나면 끝이었는데 build.gradle.kts에서는

```kotlin
versionCode = 1
versionName = "1.0"
```

처럼 등호 내지는

```kotlin
minSdkVersion(24)
```

처럼 괄호로 표현하게 바뀌는 부분이 있었다. 그래서 상황에 따라 저런 부분도 감지하게 정규식을 수정해야 했다.


등호감지
```ruby
Regexp.new(/\s*(?<key>#{params[:key]}\s*=\s*)(?<left>[\'\"]?)(?<value>[a-zA-Z0-9\.\_]*)(?<right>[\'\"]?)(?<comment>.*)/)
```

괄호감지
```ruby
Regexp.new(/\s*(?<key>#{params[:key]}\s*\(\s*)(?<left>[\'\"]?)(?<value>[a-zA-Z0-9\.\_]*)(?<right>[\'\"]?)(?<comment>.*\).*)/)
```

생각보다 간단하게 수정이 되긴 했는데 해당 플러그인에 이것저것 상태 값을 넣자니
루비를 처음 접한 때라 Rakefile이나 rubocop이 뭔지도 모르고 저거만 띡 수정해서 올려도 되나 걱정이 됐다..

그래서 당장 쓸 것만 넣어서 적당히 배포해서 쓰고 있으면 나중에 저 사람이 고쳐주겠거니 하고 적당히 배포했다.
졸지에 안드로이드 라이브러리는 하나도 없는데 루비부터 배포해본 개발자가 됐다..
아무튼 그렇게 지나갔는데 벌써 1년 전이다.

사연있는 플러그인은 [이쪽](https://github.com/zmunm/fastlane-plugin-android-versioning-kts)인데
내가 루비를 공부해서 이 프로젝트를 깔끔하게 정리하는 것보다 남이 만들어 주는 게 빠르지 않을까?
빨리 아카이브하고 싶다..

누군가 멋진 플러그인을 만들어 주기를..
