---
title: 🎉 깃허브 블로그 시작하기
date: 2024-02-20 03:50:00 +09:00
categories: etc
tags: [첫 글, 내일배움캠프]

comments: false
image:
  path: /assets/img/october-4-2019-goods-south-field.jpg
---

> #### 240219 월요일
>
> - Node.js 심화주차 1-1 ~ 1-14
>   - 객체지향, 계층형 아키텍처 패턴, 제스트 찍먹
> - 모던 자바스크립트 딥 다이브
>   - 16장 프로퍼티 어트리뷰트 복습

<br>

## 별로 궁금하진 않을 이야기

---

공부 기록용으로 계속 노션을 써 왔는데, 처음엔 노출이 적다는 이유로 노션을 선택했지만, 시간이 지나고 보니 생각이 바뀌어 똥글이어도 노출이 되었으면 싶었다. 티스토리는 개인적으로 질려서 선택지에 없었고, 무난히 벨로그로 옮기려고 했 는 데...

예~전에 가입한 계정에 뭔 짓을 해도 로그인이 안 됐다. 그 아이디는 쓰고 싶고, 그래서 보낸 컨텍 메일은 일주일째 답장이 없고. 결국 깃허브 블로그를 선택했다.

어쩔 수 없이 선택한 느낌인데... 어느정도 맞다. 회원 가입만 하면 되는 기존에 존재하는 사이트들과는 다르게 깃허브 블로그는 테마를 받아온다고 해도 처음 보는 기술들(루비, 지킬 등), 직접 설정해야 하는 부분, 배포 과정 등 번거로워서 기피하긴 했다.

그치만,  
그냥 했고,  
정말 시간이 좀 걸렸지만, (이것저것 만져보다 3시간)  
**결과물을 보니 만족스러움!**

<br>

## **맥에서 깃허브 블로그 시작하기**

---

윈도우에서 찍먹해 봤을 땐 블로그 글 하나만 따라가도 어느정도 되는 것 같았는데, 역시 맥은...

> 이 글의 목적은 그저 기록이기 때문에 간략하게 작성되어있음

### 1. Ruby 설치하기

깃허브 블로그 국룰인 지킬이라는 정적 사이트 생성기를 쓰려면 먼저 루비를 다운 받아야 한다.

윈도우 같은 경우엔 <a href="https://rubyinstaller.org/downloads/">RubyInstaller</a>로 바로 원하는 버전을 설치할 수 있지만, 맥은 아니다. 내 경우엔 일단 기본적으로 ruby가 깔려 있었지만 구버전이라 업데이트가 필요했다.

```zsh
$ brew install rbenv

$ rbenv install -l

$ rbenv install 3.x.x
```

`rbenv` 라는 루비 버전 관리자를 먼저 설치하고, 설치 가능한 ruby 버전의 리스트를 출력한 뒤 거기서 골라 설치하면 된다. `2.5.x` 이상이기만 하면 괜찮다는 말이 있었지만 나는 `2.6.x`에서 최신 권장 버전 `3.2.3`으로 업데이트 하였다.

> `ruby -v` 를 터미널에 입력했을 때 계속 구버전으로 뜨는 문제
{: .prompt-warning}

```zsh
$ rbenv init
eval "$(rbenv init - zsh)"
```

환경 변수를 설정해줘야 한다. `rbenv init`을 했을 때 나오는 코드를 `~/.zshrc`에 넣어주면 된다.  
`vim`으로 저 파일을 열고, `i`를 눌러 INSERT 모드로 바꾼 뒤, 붙여 넣고, ESC, `:wq`로 저장하고 빠져나오면 된다.

### 2. Jekyll 설치하기

```zsh
$ gem install jekyll
$ gem install bundler
```

크게 번거롭지 않다. `bundler`는 gem들의 의존성을 관리해주는데 여기서 gem은 ruby에서의 라이브러리 같은 것이라고 이해하면 된다. 잘 설치 됐는지 `jekyll -v`로 확인도 해주자.

### 3. 테마 정하기 (급완결)

처음엔 <a href="https://jekyllthemes.io/free">테마 사이트</a>에서 골라 zip 파일을 직접 다운 받고 설정하는 방식을 따르려고 했는데, 지금 사용중인 이 테마가 꽂혀서 **<a href="https://chirpy.cotes.page/posts/getting-started/">chirpy doc</a>** 이걸 보고 차근차근 따라했다. 내가 선택한 건 chirpy starter로 기본 chirpy 테마와 비교하면 빠진 파일들이 많긴 한데, 크게 커스터마이징 하고 싶은 것도 없고 신경 쓸 여력도 안 돼서 그냥 이렇게 사용하기로 결정했다.

저 Chirpy 공식 문서가 정말 친절하고 자세해서 이 단계부턴 저것만 그대로 따라가도 된다. 초기 설정, 댓글 기능, 파비콘, 문법 등 진짜 다 써 있다. 저 테마를 사용하지 않을 거여도 jekyll을 사용해 블로그를 만들 거라면 저 문서는 꼭 보기를 추천한다. 이 글은 일단 여기서 마치고 나중에 더 쓸 일이 있으면... 또 쓰겠다.

<br>

## 다음에는

아직 풀리지 않은 문제들을 잘 해결해보고 포스팅하겠다. (라고 말했지만 3/2 마지막 수정만 하고 관련한 포스팅은 더 안 할 예정) (이라고 했지만 할 수도 있음)

- 분단위 날짜가 적용이 안 되어서 수동으로 적어줘야 함 ㄱ-
- url을 지정된 값으로만 바꿀 수 있는데(title, pathname, ...) 마음에 안 들어서 고민 중  
  => 먼저, 현재 post할 때 md 파일 이름을 `YYYY-MM-DD-title` 이런 식으로 저장하고, md 최상단에도 title, date 등 써주고 있다. 나는 **url은 영어로 나오게끔 하고 페이지에서 봤을 때 title은 한글로 나오기**를 바랐다. 해당하는 포스트의 파일 이름의 title은 영어로 작성하고 그 파일 내부에서의 title은 한글로 작성하면 내가 원하는 의도대로 잘 된다.
  - 하지만 파일 이름의 날짜가 달라도 title이 같으면 최근 걸로 덮어 씌워진다는 문제가 있었다.  
    => 포스트 링크를 식별할 때 title만 따져서 이런 문제가 생긴 거였다. `_config.yml` 파일의 permalink 옵션을 `posts/:y_day/:title`로 y_day를 추가하였다. 매년 같은 날에 같은 타이틀 글만 안 쓰면 된다ㅋㅋ (<a href="https://jekyllrb.com/docs/permalinks/">참고</a>)

---

시간 써서 만든 만큼 열심히 포스팅 해 보겠음!

### 참고한 것들

- **<a href="https://f-lab.kr/blog/developer-blog-tips">면접관 관점에서 본 좋은 개발자 블로그</a>**
- <a href="https://devpro.kr/posts/Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-(1)/">깃허브 블로그 만들기 (1)</a>
- <a href="https://www.ruby-lang.org/ko/documentation/installation/">루비 설치 공식 문서</a>
- <a href="https://codecamper.me/blog/122/#footnote_1">루비 버전 업데이트</a>
