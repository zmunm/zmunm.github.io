---
title: StopShip이 aab 빌드에선 작동하지 않음
layout: single
categories:
  - android
tags:
  - android
  - gradle
  - lint
---
프로덕션에는 절대 들어가면 안 되지만 개발, QA를 위해 소스를 조작해야 하는 경우가 있다.
이런 경우에는 StopShip을 이용하여 해결했었는데 apk를 만들 때는 잘 동작하다 aab를 만들 때는
먹통이 됐다.

stopship에 대한 체크는 `app:lintVitalRelease`에서 돌아가는데 이게 `app:assembleRelease`에서만
포함되고 `app:bundleRelease`에서는 포함되지 않는 게 문제였다.

왜 bundle task에는 lintVital을 체크하지 않는 건지 모르겠지만, 우선은 간단하게 dependOn으로 해결했다..


---

[StopShip에 대해 설명된 포스트](https://naveentp.medium.com/lints-stopship-can-save-you-from-pushing-your-buggy-code-to-production-4fa0db40d9b1)
