---
layout: single
categories:
  - ruby
tags:
  - ruby
  - jekyll
---
Welcome To Jekyll은 jekyll 가이드를 따라서 `jekyll new blog`를 하면 최초로 만들어주는 페이지다.
기념 삼아서 이 페이지에 jekyll 블로그를 구성해 본 후기를 쓴다.

# 왜 지킬을 골랐나?
사실 꽤 예전에 jekyll 블로그를 시도했었는데 그때는 지금보다도 더 아무것도 모를 때라 어디서
보고 jekyll template을 포크떠서 이리저리 조작해 봤었다.
그러다 보니 디자인도 깔끔하게 안 나오고 피로는 두 배로 쌓여서 금방 드랍했다..

이번에 다시 지킬 블로그를 해보자고 생각하게 된 계기는 이거다.

1. `gradlew dokkaJekyll`
2. `gradlew dokkaJekyllMultiModule`

원래도 dokka가 출력 포맷으로 markdown은 지원해주고 있었지만, 멀티모듈에서 jekyll로 사용하려면
이것저것 손이 많이 가서 깊게 건드리지 않고 있었는데 이번에 알파버전에서 아예 태스크가 생겼다.

미디움도 포스타입도 요즘 좋다는 노션도 있지만 역시 소스로 짜는 블로그는 각별한 맛이 있는 것 같다.
블로그에 정보를 하나하나 쌓아가려면 귀찮고 자꾸 미루게 될 것 같아서
아예 다른 소스 작업을 할 때 documenting 하듯이 겸사겸사 하도록..
문서작업 알레르기가 있는 나한테는 이런 형태가 좋은 것 같다.


# 어떻게 구성할까?
처음에는 dokka 태스크나 한번 써볼까 시작했는데 지금 그리는 그림은 이렇다.

1. Jekyll theme를 커스텀하게 하나 들고 있기
2. Git hub 계정의 메인 블로그를 만들기
3. Library Repository의 docs를 만들기
4. 메인 블로그와 Library블로그를 같은 테마로 이어지는 것처럼 보이게 만들어주기

이번에는 테마가지고 삽질 안 하려고 이것저것 기능이 많아 보이는 [Minimal mistakes](https://mmistakes.github.io/minimal-mistakes/)로 골랐다.
사실 오래 살펴본 건 아니고 그냥 딱 삘이 와서 골랐는데 삶에서 중요한 선택이란 게 다 그런 거 같다.
이름부터가 내 시간을 줄여줄 것만 같아서 믿음직스럽다.

실제로 가장 골치 아플 것 같았던 검색이나 댓글 같은 기능이 아주 간단하게 해결됐다.
다들 disqus만 쓰니까 이런 기능이 있는 줄 몰랐는데 GitHub 이슈와 댓글을 연동할 수 있었다.
사용성은 disqus가 더 좋은 것 같지만 그냥 내 마음에 쏙 들어서 바로 설정했다.


이제 기본적인 환경은 구성됐으니 글감을 찾아봐야 한다.
