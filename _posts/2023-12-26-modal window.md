---
title: 모달창 구현하기
date: 2023-12-26 22:14:00 +09:00
categories: [javascript, FE-vanilla]
tags: [Spartify, 내일배움캠프]
---

> notion에 작성한 글 옮겨오기
{: .prompt-info}

<br>

> #### 231226 화요일
>
> - 팀 프로젝트 진짜 시작
> - TIL 특강

## 모달창 구현하기

<hr>

### html, script

```html
<body>
  <button type="button" class="openBtn">작성하기</button>
  <div class="modal">
    <div class="modal_body">
      <div class="inputBox" id="inputBox">
        <!-- 경훈님의 인풋박스 코드 -->
      </div>
      <button class="inputBtn" id="inputBtn">작성 완료</button>
      <button class="cancelBtn">취소</button>
    </div>
  </div>
  <script>
    const modal = document.querySelector(".modal");
    const modalOpenBtn = document.querySelector(".openBtn");
    const modalCloseBtn = document.querySelector(".cancelBtn");

    modalOpenBtn.addEventListener("click", () => {
      modal.style.display = "flex";
    });

    modalCloseBtn.addEventListener("click", () => {
      alert("작성이 취소되었습니다.");
      modal.style.display = "none";
    });
  </script>
</body>
```

- `document.querySelector('.class')` : 괄호 속 클래스를 사용하는 첫 번째 요소를 반환

- `addEventListener("click", () => {}` : “click” 이벤트 시 함수 실행 (괄호 안 익명함수(화살표 함수)로 실행)

- 모달창은 클릭 시에 생겨나야 하기 때문에 평소엔 display값을 none으로 두고, openBtn 클릭 시 display 값을 flex로, 작성 취소/완료 시 다시 none으로 바꾼다. (완료 버튼은 데이터베이스와 연결해야 하기 때문에 헤드 부분 스크립트(속성 모듈)에 구현되어있다!)

### css

```css
/* 모달창 */
.openBtn,
.inputBtn {
  font-family: "Nanum Gothic", sans-serif;
  background-color: #9281bc;
  color: white;
  border-color: transparent;
  border-radius: 5px;
}

.cancelBtn {
  background-color: white;
  color: black;
  border-color: #9281bc;
  border-radius: 5px;
}

.modal {
  position: absolute;
  display: none;

  justify-content: center;

  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.2);
}

.modal_body {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);

  width: 500px;
  height: 670px;
  padding: 30px;

  text-align: center;

  background-color: #d9d2ea;
  border-radius: 10px;
}
```

- 일단 위 버튼은 안 봐도 될 것 같고, modal이랑 modal_body만 보면 된다.

- `.modal`

  - 최상위 부모 요소(body)위에 겹치게 뜨도록 해야 하기 때문에 position은 absolute
  - 상술하였듯 평소엔 보이지 않아야 하기 때문에 display는 none
  - 모달창에 들어간 걸 확실하게 하기 위해 부모 페이지를 흐릿하게 하려고 background-color를 전체적으로 그림자처럼 넣었다.

- `.modal_body`
  - 최상위 부모 요소(body) 위에 겹치게 position absolute
  - 50%씩 이동하는 중앙 정렬 코드이다. 저 세 줄이 세트인 건 알겠는데 당최 뭔 의미인진 구글링 해봐도 이해가 잘 안된다.

<br>

## 오늘의 커밋

<hr>

<img src="/assets/img/231226 commit log.png" alt="231226 commit log">

- 또드미 또수정. 이제 완성 전까지는 건들 일 없을 듯..?
- 경훈님은 모달창 안에 들어갈 인풋박스를 구현, 나는 모달창을 구현하기로 분담하였다.
- 비주얼적인건 다 구현하고 데이터베이스 연결을 하려는데 안 되어서 경훈님께 헬프쳤다.  
  ➡️ 변수명에 오타가 있어서 데이터 저장이 제대로 안되는거였다…^^ 그것도 수정!
- 기본적인 틀과 데이터 연결까지 모두 구현 완료!  
  원래는 작성페이지를 아예 따로 만들려고 했는데 그냥 버튼 하나에 모달창 나오는 식으로 바꾸었다. 이제 메인페이지가 어느정도 만들어지면 이 버튼을 메인으로 옮기면 된다.

<br>

## 오늘의 회고

<hr>

- 오전엔 팀원 분들과 회의+구글 슬라이드로 어떻게 만들지 확실하게 결정과 분담 후, 11시쯤부터 바로 작업을 시작했다. 작성 페이지는 두 명, 메인 페이지는 세 명으로 나는 경훈님과 작성 페이지를 맡게 되었다. 점심시간 끝나고 두 시부터 30분간 TIL 특강도 들었다.

- 레포 이름을 가제였던 `node4th-music-is-my-life-web` 에서 `node4th-spartify-web` 으로 변경하면서 풀/푸시에 좀 문제가 생기길래 **`git remote set-url origin <새로운 주소>`** 로 새로 설정했다.

- 내일은 웹개발 AtoZ 5주차까지 다 완강하고, 자바스크립트 문서를 다시 봐야겠다.

### **고민해결**

저번 주에 고민하던 브랜치가 어쩌고 분담이 어쩌고… 그냥 한 브랜치에서 병합하고 하면서 얼렁뚱땅 진행중이다ㅋㅋㅋ 노란 글씨의 향연이지만 어쨌든 진행은 되고 있으니까 다행이다.
