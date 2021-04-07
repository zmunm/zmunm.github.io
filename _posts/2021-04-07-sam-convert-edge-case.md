---
title: SAM 변환의 함정
layout: single
categories:
  - kotlin
tags:
  - kotlin
  - lint
---
하나의 Interface 안에 하나의 function 만 있는 경우에는 항상 SAM 변환이 가능한 줄 알았는데 아니었다.
변환할 수 없는 경우도 있고 변환하면 안되는 경우도 있었다.

## 변환할 수 없는 경우?
필자는 SAM 변환 이전의 anonymous object와 변환 이후의 lambda가 완전히 같고 보일러플레이트만 사라지는
것이라고 잘못 알고 있었다.. 문장에서도 알 수 있듯이 object에서 lambda로 근본이 달라진다.

1. 익명 오브젝트에 불순물이 들어간 경우
```kotlin
object: Runnable {
  val number: Int = 2 // property가 들어가거나
  fun foo() {} // 자신만의 함수가 있거나
  init {} // init 블록이 있는 경우
  override fun run()
}
```
lambda가 가질 수 없는 걸 anonymous object안에 선언해둔 경우에는 변환할 수 없다.

2. this 레퍼런스를 사용하는 경우
```kotlin
object: Runnable {
  override fun run(
    this
  )
}
```
object는 this를 사용할 수 있지만 lambda에는 this가 없으니 당연히 사용하지 못한다.

하지만 아래와 같은 경우에는 기똥차게 바꿔준다.
```kotlin
fun interface Sam {
    fun foo()
}
class Target {
    init {
        object : Sam {
            override fun foo() {
                this@Target
            }
        }
    }
}
```
분명 this를 사용하고 있지만 자신의 this가 아니라고 라벨로 달아주면 잘 바꿔준다.

3. object가 들고 있는 함수를 호출하는 경우
```kotlin
object: Runnable {
  override fun run(
    hashCode()
  )
}
```
hashCode, toString처럼 object가 가지고 있는 함수를 호출하는 경우에도 변환하지 못한다.
lambda는 class instance가 아니기 때문에 자체적인 hashCode가 없다.


```kotlin
object: Runnable {
  override fun run(
    run()
  )
}
```
이렇게 재귀적으로 부르는 경우에도 변환할 수 없다.


## 변환하면 안되는 경우?
변환할 수 없는 경우는 IDE가 잘 알려주니까 사실 몰라도 되지만 IDE가 바꾸라고 하지만 실은 바꾸면
안되는 경우가 있다. 이렇게 생긴 버그는 잡아내기도 어렵고 마주하면 아주 스트레스받는다.

바로 레퍼런스를 직접 확인하는 경우인데 먼저 케이스를 살펴보자.

```kotlin
fun interface Sam {
    fun foo()
}
fun newObject() = object : Sam {
    override fun foo() {
    }
}
fun lambda() = Sam {}
val a = newObject() === newObject() // false
val b = lambda() === lambda() // true
```
??? anonymous object는 항상 새로운 instance를 생성하지만 SAM으로 변환하면 kotlin이 컴파일 할 때
일종의 singleton으로 처리해서 같은 reference를 갖게 된다. 그래서 reference equal체크를 하면 안된다.

이런 경우가 뭐 있을까 싶기도 하지만 내가 몰라도 라이브러리에서 사용하는 경우가 의외로 있었다.
바로 [LiveData.observe](https://stackoverflow.com/questions/47025233/android-lifecycle-library-cannot-add-the-same-observer-with-different-lifecycle)
인데 Observer를 lambda로 변환하고 내용을 비워두면 에러가 발생한다.
LiveData 내부적으로 reference를 키로 사용하기 때문인데 매번 발생하는 것도 아니고
개발중에 갑자기 이러면 정말 난감하니 미리 알아두자.

번외로 [코틀린 개발자들도 헷갈려 하나보다](https://github.com/JetBrains/kotlin/commit/99a6bde)



 * https://kotlinlang.org/docs/java-interop.html#sam-conversions Java Interface의 SAM 변환
 * https://kotlinlang.org/docs/fun-interfaces.html fun interface
