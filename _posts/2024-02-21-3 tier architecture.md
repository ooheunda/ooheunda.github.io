---
title: 3계층 아키텍처 데이터 가공하기
date: 2024-02-22 07:50:00 +09:00
categories: [javascript, express.js]
tags: [architecture, 내일배움캠프]
---

> #### 240221 수요일
>
> - 개인과제
>   - 3 layered architecture

<br>

## **DB 데이터 가공을 어디서 해야할까?**

> router 파일 하나에 모든 로직을 싹 때려박은 예전 과제를 3계층 아키텍처로 분리하는 과정  
> findAllResumes (이력서 목록 조회)
{: .prompt-info}

```javascript
// src/services/resumes.service.js
export class ResumesService {
  constructor(resumesRepository) {
    this.resumesRepository = resumesRepository;
  }

  findAllResumes = async (sort) => {
    sort = sort || "createdAt";

    const resumes = await this.resumesRepository.findAllResumes(sort);

    if (!resumes[0]) throw new Error("이력서 데이터 없음");

    return resumes.map((resume) => {
      return {
        resumeId: resume.resumeId,
        title: resume.title,
        status: resume.status,
        name: resume.user.name,
        createdAt: resume.createdAt,
        updatedAt: resume.updatedAt,
      };
    });
  };
}
```

강의에선 저렇게 service 계층에서 가공해 controller로 보내주어서 저게 무조건 정답이라고 생각했다.

```javascript
// src/repositories/resumes.repository.js
export class ResumesRepository {
  constructor(prisma) {
    this.prisma = prisma;
  }

  findAllResumes = async (sort) => {
    const resumes = await this.prisma.resumes.findMany({
      orderBy: {
        [sort]: "desc",
      },
    });

    return resumes;
  };
}
```

그런데 직접 작성하다보니 처음부터 repo 계층에서 select해 데이터를 불러오면 나중에 가공할 일도 없고 더 좋지 않을까라는 단순한 의문이 들었다. 구조적인거라 혼자 더 고민해봤자 시간 낭비일 것 같아서, 다른 분께 여쭤보다 결국엔 튜터님께 갔다.

일단 결론은, 무조건적인 정답은 없다.

repo 계층에서 select를 써서 처음부터 데이터를 그것만(`findAllResumes`에 필요한 것들만) 가져오게 되면, **범용성**이 떨어지게 될 것이다. 하지만 `select * Resumes`보다 `select resumeId, title ... from Resumes`가 **속도**가 당연히 더 빠른 장점은 있다. 그래서 이건 어떻게 사용하게 될지에 따라 다르다.

그리고 내 진짜 문제는 약간 다른 결이었는데, 위 코드대로 돌려보니 `resume.user.name`에서 프로퍼티 리딩 에러가 났다. `Resumes` 테이블의 필드들뿐만 아니라 관계 설정된 `Users` 테이블의 필드까지 필요했기 때문에 repo에서 select를 해줘야 했다.

```
model Resume {
  ...
  user Users @relation(fields: [userId], references: [userId], onDelete: Cascade, onUpdate: Cascade)
}
```

스키마에서 user 컬럼이 있는 걸 대충 보고 암묵적으로 user도 같이 가져오겠거니... 했는데 저 코드는 그냥 관계 설정용이고, `fields: [userId]`를 보면 알 수 있듯 갖고 있는 건 userId뿐이다. 스키마 짠지 얼마나 됐다고 이런 기본적인걸 헷갈리고 있다.

`fields`랑 `reference`에 value가 배열로 들어가는데 저기에 name도 추가하고 테이블 컬럼으로 name도 추가하면 될 것 같긴 하다. 근데 이건 또 DB 구조적으로 안 좋은건가 싶기도 하고; 잘 모르겠다.

```javascript
// src/repositories/resumes.repository.js
findAllResumes = async (sort) => {
  const resumes = await this.prisma.resumes.findMany({
    select: {
      resumeId: true,
      title: true,
      status: true,
      user: {
        select: {
          name: true,
        },
      },
      createdAt: true,
      updatedAt: true,
    },
    orderBy: {
      [sort]: "desc",
    },
  });

  return resumes;
};
```

아무튼 이렇게 repo에서 가공(애초부터 select해오기)하는 걸로 수정했고, 잘 돌아간다.  
select와 관련된 데이터 가공은 상황에 따라 다른데 orderBy(정렬)는 웬만하면 repo에서 하는게 맞다고 하셨다.

<br>

### 참고한 것들

- 빛현우 튜터님
- <a href="https://www.ibm.com/kr-ko/topics/three-tier-architecture">3계층 아키텍처란?</a>
