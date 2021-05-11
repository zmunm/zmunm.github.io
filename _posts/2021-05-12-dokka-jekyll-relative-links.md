---
title: Jekyll Relative Links 플러그인
layout: single
categories:
  - ruby
tags:
  - ruby
  - jekyll
  - dokka
  - kotlin
---
`gradle dokkaJekyll`로 생성한 `~/*.md` 파일을 jekyll이 파싱하면 html로 확장자가 바뀌어서
링크가 끊겨버리는 버그가 있었다.

해결은 [이 플러그인](https://github.com/benbalter/jekyll-relative-links)을 설치하는
걸로 간단하게 됐지만 찾는 과정은 그렇지 않았다.

```md
[foo](bar.md)
```

이렇게 생긴 링크를

```md
[foo](bar.html)
```

이렇게 바꿔준다.


이런건 기본으로 해줘야하지 않나..
