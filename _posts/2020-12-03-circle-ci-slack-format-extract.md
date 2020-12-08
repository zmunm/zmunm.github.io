---
title: CircleCI slack orb 에서 사용하는 json template을 외부 파일로 뽑아내기
layout: single
categories:
  - CircleCI
tags:
  - CircleCI
  - slack
---
Circle CI가 새로운 UI로 이전하면서 기존에 웹훅만 걸어서 success fail 메세지를 던져주던 기능을 없애버렸다.
그래서 Circle CI에 slack integration을 넣으려면 무조건 slack orb를 사용해야 하는 상황이 찾아왔다.


# 무엇이 달라지나?

기존에는 모든 work flow가 끝난 이후에 success fail 하나씩만 던져줬다. 하지만 slack orb에서는
각 task마다 slack notify를 할지 말지 설정해줘야 했다.

그리고 어떤 형태로 slack message를 꾸며줄지도 직접 설정할 수 있었다.

slack message template은 [Block Kit Builder](https://api.slack.com/tools/block-kit-builder)로
검색하면 많이 나온다. 여기서 Json Format을 만들 수 있고 task마다 설정해야 하니까 이 json template이
여러 번 들어가게 된다.

이번 포스팅의 주제는 이 template을 어떻게 재사용하는지에 대해서다.

# 어떻게 재사용할까?

json을 재사용하려면 어떤 형태로든 json을 변수에 넣어야 한다. CircleCI는 ENVIRONMENT를 제공하니
그쪽으로 넣으면 간단하겠지 하고 생각했다. 기나긴 삽질을 하기 전까지는

## ENVIRONMENT에는 어떻게 넣지?
다른 방법도 있겠지만 크게 두 가지 방법을 떠올렸다.

첫번째는 CircleCI 설정에서 그냥 환경변수로 집어넣는거다.
이러면 당장 쉽고 빠르게 적용할 수 있다.

하지만 template을 수정하려면 기존 ENVIRONMENT를 읽을 수 없으니
어딘가에 template을 들고 있어야 한다. 이게 싫었다. template은 코드로 관리하면서 변경하고 싶은 게
생기면 바로 반영할 수 있게 환경을 구성하고 싶었다.

두번째는 json을 외부 파일로 저장하고 CircleCI에서 불러와 적용하는 건데 이게 좋아 보인다.


# slack json template을 외부 파일에서 불러오는 방법
1. 일단 json template이 저장된 경로를 `.circleci/template/slack.json` 이라고 가정하자. 취향대로 설정하면 된다.
2. command step으로 파일을 읽어서 환경변수에 저장한다. CircleCI의 ENVIRONMENT 변수는 command shell의 환경변수와 연동된다.
그래서 이렇게 밀어 넣은 값을 쓸 수 있다.
3. `slack/notify` step에서 template 필드에 방금 설정한 환경변수를 입력한다.

> 와!! 정말 간단해 보인다. 그럼 이제 구현해보자.

## 당연하지만 orb를 설정한다.

```yml
orbs:
  slack: circleci/slack@4.1.1
```

## 여러 곳에서 사용할 스크립트니까 command로 만들어 놓자

```yml
commands:
  set_slack:
    steps:
      - run:
          name: Set Message Info
          when: always
          command: |
            SLACK_SUCCESS=$(cat .circleci/template/slack.json)
            echo "export SLACK_SUCCESS='${SLACK_SUCCESS}'" >> $BASH_ENV
            source $BASH_ENV
```

혹시 까먹을 수 있으니까 command만 한 줄 한 줄 살펴보자.

template 파일을 불러와서 변수에 넣어두기
```sh
SLACK_SUCCESS=$(cat .circleci/template/slack.json)
```

BASH_ENV로 template string을 넣어두기. 작은따옴표 큰따옴표가 중요하다.
```sh
echo "export SLACK_SUCCESS='${SLACK_SUCCESS}'" >> $BASH_ENV
```

적용하기
```sh
source $BASH_ENV
```

## job 에서 써보기

성공했을 때만 slack을 쏘기
```yml
jobs:
  build:
    steps:
      - checkout
      - set_slack
      - slack/notify:
          event: pass
          template: SLACK_SUCCESS
```

## 합쳐본 형태는 대충 이렇다.

필요한 image등을 잘 섞어 넣으면 된다.
```yml
orbs:
  slack: circleci/slack@4.1.1

commands:
  set_slack:
    steps:
      - run:
          name: Set Message Info
          when: always
          command: |
            SLACK_SUCCESS=$(cat .circleci/template/slack.json)
            echo "export SLACK_SUCCESS='${SLACK_SUCCESS}'" >> $BASH_ENV
            source $BASH_ENV

jobs:
  build:
    steps:
      - checkout
      - set_slack
      - slack/notify:
          event: pass
          template: SLACK_SUCCESS

workflows:
  version: 2
    build_and_test:
      jobs:
        - build
```


CircleCI 는 자꾸 뭘 갑자기 바꿔서 안 해도 될 일을 하게 만든다..
빨리 GitHub Action을 제대로 써보고 싶다.
