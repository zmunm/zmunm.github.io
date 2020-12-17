---
title: 복잡한 구조의 gradle project에서 라이브러리 버전관리하기
layout: single
categories:
  - gradle
tags:
  - android
  - gradle
  - gradle-kotlin-dsl
---
gradle 옵션이 복잡해지면 IDE가 라이브러리의 새로운 버전이 있는지 알려주지 못하는 경우가 많다.
multi module project라던가.. dependency.gradle을 뽑아냈다거나 buildSrc랑 kotlin-dsl도 쓰면 완벽하게 못찾는다.

그럴 때 [ben-manes/gradle-versions-plugin](https://github.com/ben-manes/gradle-versions-plugin) 을 사용하면
쉽게 해결할 수 있다. 사용방법이 어렵지도 않아서 README.md 만 쫓아가다 보면 바로 적용할 수 있다.

오늘 시점으로 최신버전이 0.36.0 이라 살짝 불안하긴 하지만 이미 굵직굵직한 라이브러리나
프로젝트에서도 많이 쓰고 있는 걸 보니 아직 대체제가 없는 모양이다. 넣었다 빼기 어려운 것도 아니고
2.6k의 스타수가 믿음직스러우니 적용해보자..

# buildSrc로 custom gradle plugin을 만들어서 쉽게 적용하기

이게 진짜 포스팅을 한 이유다. 프로젝트의 README는 누구나 사용가능하기 쉽게 적어놔서 이미 buildSrc를 적용한 프로젝트에선 별로 매력적이지 않다.
그래서 좀 더 깔끔하게 gradle-versions-plugin 을 적용하는 방법을 적어둔다.


### /buildSrc/build.gradle.kts

```kotlin
plugins {
    `kotlin-dsl`
}

repositories {
    jcenter()
}

dependencies {
    implementation("com.github.ben-manes:gradle-versions-plugin:0.36.0")
}
```

이렇게 buildSrc 의 build.gradle.kts에서 implementation으로 넣어두면 :buildSrc 모듈에서도
이 플러그인을 사용할 수 있고 project level build.gradle에서 classpath나 repositories를 따로 적어주지 않아도 된다.

### /buildSrc/src/main/java/versioning.gradle.kts

```kotlin
import com.github.benmanes.gradle.versions.updates.DependencyUpdatesTask

plugins {
    id("com.github.ben-manes.versions")
}

fun isNonStable(version: String): Boolean {
    val stableKeyword = listOf("RELEASE", "FINAL", "GA").any { version.toUpperCase().contains(it) }
    val regex = "^[0-9,.v-]+(-r)?$".toRegex()
    val isStable = stableKeyword || regex.matches(version)
    return isStable.not()
}

tasks.named("dependencyUpdates", DependencyUpdatesTask::class.java).configure {
    // using the full syntax
    resolutionStrategy {
        componentSelection {
            all {
                if (isNonStable(candidate.version) && !isNonStable(currentVersion)) {
                    reject("Release candidate")
                }
            }
        }
    }
}
```

isNonStable이나 task configure는 프로젝트의 README에서 가져왔다. 중요한 부분은 이 파일의 이름인데
abc.gradle.kts 이런 식으로 파일명을 지어두고 싱크를 돌리면 .gradle.kts가 지워진 `abc` 가
커스텀 플러그인으로 사용가능하게 생성된다. 그러면 다른 곳에서 사용하기 아주 간단해진다.

### /build.gradle.kts

```kotlin
plugins {
    versioning
}
```

끝났다! buildSrc 바깥의 gradle이 굉장히 간단해졌다. 이제 `gradlew dependencyUpdates` 를
사용하면 프로젝트 전체에서 사용중인 라이브러리의 최신버전을 한눈에 볼 수 있다.

version 플러그인은 프로젝트 레벨에만 적어두면 되니까 이러면 끝나는데 같은 방식으로 서브모듈마다
명시해야 하는 lint, document, test 같은 설정들을 custom gradle plugin으로 만들어두면 굉장히 편하다.
