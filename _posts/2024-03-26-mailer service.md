---
title: Nest.js nodemailer
categories: [javascript, nest.js]
tags: [to-dubu, 내일배움캠프]
---

> #### 240326 화요일
>
> - 최종 팀 프로젝트 기획

<br>

## **mailer service**

<hr>

```typescript
// src/mail/mail.module.ts

import { Module } from "@nestjs/common";
import { ConfigService } from "@nestjs/config";
import { MailerModule } from "@nestjs-modules/mailer";
import { MailService } from "./mail.service";
import { JwtModule } from "@nestjs/jwt";

@Module({
  imports: [
    MailerModule.forRootAsync({
      useFactory: (config: ConfigService) => ({
        transport: {
          host: "smtp.gmail.com",
          port: 587,
          auth: {
            user: config.get("MAILER_ID"),
            pass: config.get("MAILER_PASSWORD"),
          },
        },
        defaults: {
          from: '"todubu" <modules@nestjs.com>',
        },
      }),
      inject: [ConfigService],
    }),
    JwtModule.registerAsync({
      useFactory: (config: ConfigService) => ({
        secret: config.get<string>("MAILER_TOKEN_KEY"),
      }),
      inject: [ConfigService],
    }),
  ],
  providers: [MailService],
  exports: [MailService],
})
export class MailModule {}
```

```typescript
// src/mail/mail.service.ts

import { MailerService } from "@nestjs-modules/mailer";
import { ConflictException, Injectable } from "@nestjs/common";
import { ConfigService } from "@nestjs/config";

@Injectable()
export class MailService {
  constructor(
    private readonly mailerService: MailerService,
    private readonly config: ConfigService
  ) {}

  sendRegisterMail(userEmail: string, token: string): boolean {
    const bodyOption = this.registerBody(token);

    this.mailerService
      .sendMail({
        to: userEmail,
        ...bodyOption,
      })
      .then((result) => {
        console.log(result);
      })
      .catch((error) => {
        new ConflictException(error);
      });

    return true;
  }

  sendInvitationMail(userEmail: string, token: string): boolean {
    const bodyOption = this.invitationBody(token);

    this.mailerService
      .sendMail({
        to: userEmail,
        ...bodyOption,
      })
      .then((result) => {
        console.log(result);
      })
      .catch((error) => {
        new ConflictException(error);
      });

    return true;
  }

  private registerBody(token: string): { subject: string; html: string } {
    const url = `http://${this.config.get("HOST")}:${this.config.get(
      "PORT"
    )}/users/email-verify?signupVerifyToken=${token}`;
    return {
      subject: "가입 인증 메일",
      html: `
          가입확인 버튼를 누르시면 가입 인증이 완료됩니다.<br/>
          <form action="${url}" method="POST">
            <button>가입하기</button>
          </form>
        `,
    };
  }

  private invitationBody(token: string): { subject: string; html: string } {
    const url = `http://${this.config.get("HOST")}:${this.config.get(
      "PORT"
    )}/boards/accept/${token}`;
    return {
      subject: "보드 초대 메일",
      html: `
          새로운 보드에 초대되었습니다!<br/>
          <form action="${url}" method="POST">
            <button>초대 수락하기</button>
          </form>
        `,
    };
  }
}
```

service body 부분 pug, handlebars, ejs 등 FE적으로 확장할 수 있는데 그냥 썼다.

자세한 건 <a href="https://nest-modules.github.io/mailer/docs/mailer.html">how to use mailer</a> 여기 참고
