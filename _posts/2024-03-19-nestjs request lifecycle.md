---
title: Nest.js의 요청 흐름과 생명 주기
categories: [javascript, nest.js]
tags: [to-dubu, 내일배움캠프]

image:
  path: /assets/img/240319 nestjs cycle.png
  alt: nestjs cycle
---

> #### 240319 화요일
>
> - 과제... 해야겠지?

<br>

## **Nest.js의 요청 흐름과 생명 주기**

<hr>

> Request -> Middleware -> Guard -> Pipe -> Controller -> Service -> Repository -> Response

### **기본적인 흐름**

언어와 프레임워크를 떠나 이 순서는 본질적으로 비슷하다. 괄호 안의 단어는 nest에서의 용어를 의미한다.

1. Request

2. Middleware

3. Make sure the user is authenticated (Guard)  
   유저 인증/인가

4. Validate data contained in the request (Pipe)  
   요청에 포함된 데이터 검증

5. Route the request to a particular function (Controller)  
   요청을 특정 라우터로 연결

6. Run some business logic (Service)  
   비즈니스 로직 실행

7. Access a database (Repository)  
   필요시 데이터베이스 접근

8. Response

### Middleware

> global middlewares -> module middlewares

1. First, Nest runs globally bound middleware (such as middleware bound with `app.use`)  
   전역적으로 설정된 미들웨어 (이를테면 `app.use`에 적용한 미들웨어) 먼저 실행

2. and then it runs module bound middleware  
   모듈 단위의 미들웨어 실행
   - 바운딩된 순서대로 실행된다. express에서의 작동과 비슷하다.
   - 서로 다른 모듈에 적용된 미들웨어의 경우엔 root module에서의 미들웨어가 가장 먼저 실행되고, 그 다음엔 모듈의 imports 배열 순서대로 실행된다.

### Guards

> global guards -> controller guards -> route guards

미들웨어와 마찬가지로 바운딩된 순서대로 실행된다.

```typescript
@UseGuards(Guard1, Guard2) //controller level
@Controller("cats")
export class CatsController {
  constructor(private catsService: CatsService) {}

  @UseGuards(Guard3) //route level
  @Get()
  getCats(): Cats[] {
    return this.catsService.getCats();
  }
}
```

위 예제코드에선 (Guard1 -> Guard2) -> Guard3 순서대로 실행된다.

### Interceptors

대체적으로 Guard의 패턴을 따르나 하나 알아두어야 할 것이 있는데, interceptor는 RxJS Observable을 반환한다는 것이다. 들어오는 요청은 global -> controller -> route 순으로 진행되는 것은 같으나 이 요청에 대한 응답은 route -> controller -> global 순으로 진행된다.  
또 Pipe, Controller, Service에서 던져진 에러들은 모두 interceptor의 `catchError` 연산자가 읽을 수 있다.

### Pipes

> global pipes -> controller pipes -> route pipes

바운딩된 순서대로 실행된다. route parameter 레벨에서 여러 개의 pipe가 실행되어야 하는 경우 매개변수의 역순으로 작동된다.

```typescript
@UsePipes(GeneralValidationPipe) //controller level
@Controller("cats")
export class CatsController {
  constructor(private catsService: CatsService) {}

  @UsePipes(RouteSpecificPipe) //route level
  @Patch(":id")
  updateCat(
    @Body() body: UpdateCatDTO,
    @Param() params: UpdateCatParams,
    @Query() query: UpdateCatQuery
  ) {
    return this.catsService.updateCat(body, params, query);
  }
}
```

`GeneralValidationPipe`는 `query` -> `params` -> `body` 순서로 실행되고, 그 후 `RouteSpecificPipe`으로 흐름이 이동하게 되며 같은 순서를 거친다.

### Filters

> route filters -> controller filters -> global filters

전역이 최우선이 아닌 유일한 구성 요소로, 제일 낮은 레벨에서부터 시작하여 작동된다. 이때, 하위 레벨에서 처리된 예외는 상위 레벨에서 다시 catch될 수 없다. The only way to achieve an effect like this is to use inheritance between the filters.

필터는 catch되지 않은 예외만 잡아주어서, `try/catch`문으로 별도의 예외 처리를 한다면 필터로 넘어가지 않는다.

### **요약**

1. Incoming request
2. Middleware  
   2.1. Globally bound middleware  
   2.2. Module bound middleware
3. Guards  
   3.1 Global guards  
   3.2 Controller guards  
   3.3 Route guards
4. Interceptors (pre-controller)  
   4.1 Global interceptors  
   4.2 Controller interceptors  
   4.3 Route interceptors
5. Pipes  
   5.1 Global pipes  
   5.2 Controller pipes  
   5.3 Route pipes  
   5.4 Route parameter pipes
6. Controller (method handler)
7. Service (if exists)
8. Interceptors (post-request)  
   8.1 Route interceptor  
   8.2 Controller interceptor  
   8.3 Global interceptor
9. Exception filters  
   9.1 route  
   9.2 controller  
   9.3 global
10. Server response

<br>

## 오늘의 회고

<hr>

팀 과제를 하면서 custom guard를 만들 일이 있어 고심하다 또... 현우 튜터님께서 이 문서를 알려주셨다. _(젠장 또 빛현우튜터님이야!)_ 네스트 공식문서는 진짜 친절한 것 같다. 오늘 틸은 그냥 번역에 불과하긴 하지만 한 번 찬찬히 읽어보면서 개념을 정립하는 데에 많은 도움이 되었다. 인터셉터랑 필터는 잘 안 써봤는데 어서 해봐야...겠지?

### 참고한 것들

- <a href="https://www.udemy.com/course/nestjs-complete-developers-guide-korean">udemy NestJS: The Complete Developer's Guide</a>
- <a href="https://docs.nestjs.com/faq/request-lifecycle">nest docs: request-lifecycle</a>
- <a href="https://velog.io/@haron/NestJS-Lifecycle-Events">velog: NestJS-Lifecycle-Events</a>
