---
title: 이력서 / 포트폴리오 Jekyll로 만들어보기
layout: single
categories:
  - life
tags:
  - jekyll
---
구인 구직을 위한 플랫폼이 참 많다. 특히 IT 직군은 잡플래닛이나 잡코리아 말고도
원티드, 로켓펀치, 리멤버 등 선택지가 너무나 다양하다.

그리고 각자 이력서 노출 포맷이 다르다. 똑같은 정보를 여러 곳에 수정하기는 정말 힘들고 귀찮다.
플랫폼을 통해 지원한다면 상관없겠지만, 아직 자유양식 이력서를 받는 곳도 많다.
그래서 나만의 이력서를 만들어두긴 해야겠다고 생각했다.

이력서를 쓰려고 문서를 오랜만에 열어봤다. .docx .hwp .pdf 는 정말 익숙해지지 않는다.
나처럼 문서작업을 싫어하는 사람을 위해 이력서를 만들어주는 툴도 많지 않을까? 하고 찾아봤다.


## 간단한 폼으로 이력서를 자동으로 뽑아주는 툴들
- [JSON resume](https://github.com/jsonresume)
- [YAML resume](https://github.com/notsag/yaml-resume)
- [Markdown resume](https://github.com/there4/markdown-resume)
- [HTML resume](https://github.com/mnjul/html-resume)
- [Jekyll modern-resume-theme](https://github.com/sproogen/modern-resume-theme)

위에 세 개는 거의 비슷하다. 간단하게 폼을 작성하고 PDF로 뽑아준다.

HTML resume은 좀 더 단순하다. 날 것의 html 파일을 수정해서 브라우저의 인쇄기능으로 PDF로 만든다.

마지막 Jekyll resume 테마는 새로 jekyll 블로그를 파야 한다. 전용으로 만든다면 고려해볼 만하다.

# 뭔가 부족하다..

위의 라이브러리 모두 내가 원하는 기능이 있었지만 다 미묘했다..

1. 간단한 폼 수정으로 이력서를 지속해서 업데이트하고 싶었으며
2. 바로 PDF 파일을 뽑아내는 것보다는 브라우저로 띄울 수도 있고 인쇄기능으로 뽑아내고도 싶었고
3. Jekyll에 링크로 연결하고 테마를 입히고 싶었지만 새로운 블로그를 파기는 싫었다.

그리고 여기서 추가적인 의문이 생겼다.

> 이력서에 썼던 데이터를 웹으로 잘 꾸며내기만 하면 그걸 포트폴리오로도 쓸 수 있지 않을까?

그래서 정리하자면..

# 하나의 yml 데이터로 이력서와 포트폴리오 두 개를 뽑아내게 구성하자!

하지만 이렇게 마음에 쏙 드는 건 찾기 힘들었다. 그래서 그냥 위의 라이브러리들을 참고 해서 만들었다.


1. [yml 데이터](https://github.com/zmunm/zmunm.github.io/blob/main/_data/resume.yml)는
YAML resume을 참고했는데 국제규격 기준이라 그런지 넣고 싶지 않은 정보가 있어서 지웠고
웹 업로드 용이라 공개할 수 없는 개인정보도 지웠다.
2. [이력서](https://github.com/zmunm/zmunm.github.io/blob/main/_layouts/resume.html)
[테마](https://github.com/zmunm/zmunm.github.io/blob/main/assets/css/resume.css)는
html resume을 참고했다. 출력용이라 그런지 웹으로 보기엔 폰트가 너무 작기도 해서 입맛에 맞게 수정했다.
3. 포트폴리오 테마는 바로 만들지는 못했다.. 부족한 정보를 예쁘게 꾸미자니 미묘해서..
개인프로젝트라도 좀 진행하고 테마로 만들어 보기로 했다.

[그렇게 완성한 이력서..](https://zmunm.github.io/resume/)
하지만 뭔가 부족하다.. 역시 이력서는 생긴 것보다는 내용이 중요한데 뭐 적을 게 없는 것 같다.

오픈소스좀 열심히 하자..
