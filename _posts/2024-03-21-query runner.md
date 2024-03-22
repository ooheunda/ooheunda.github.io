---
title: Query Runner로 트랜잭션 하기
categories: [javascript, nest.js]
tags: [todubu, 내일배움캠프]
---

> #### 240321 목요일
>
> - 과제... 겠지?????

오늘은 김성민님식 틸을 쓰도록 하겠습니다...

<br>

## **Query Runner**

<hr>

```typescript
export class BoardService {
  constructor(
    @InjectRepository(Board) private readonly boardRepo: Repository<Board>,
    @InjectRepository(BoardMember)
    private readonly boardMemberRepo: Repository<BoardMember>,
    @InjectRepository(User) private readonly userRepo: Repository<User>,
    private readonly dataSource: DataSource
  ) {}

  async createBoard(userId: number, createBoardDto: CreateBoardDto) {
    const queryRunner = this.dataSource.createQueryRunner();

    try {
      queryRunner.connect();
      queryRunner.startTransaction();

      const newBoard = await queryRunner.manager
        .getRepository(Board)
        .save(createBoardDto);
      await queryRunner.manager.getRepository(BoardMember).save({
        board_id: newBoard.id,
        user_id: userId,
        role: BoardRole.OWNER,
      });

      queryRunner.commitTransaction();
      // commit을 하면 자동으로 release 되는듯하다.
      // 원래는 finally에 release를 넣었는데 그러면 없는 쿼리러너에 또 요청을 준다는 에러가 떴었다.

      return newBoard;
    } catch (err) {
      queryRunner.rollbackTransaction();
      queryRunner.release();
    }
  }
}
```
