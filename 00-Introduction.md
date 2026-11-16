---
title: NestJS — Introduction
tags:
  - nestjs
  - backend
  - introduction
status: Done
updated: 2026-10-10
source: https://docs.nestjs.com/
---

# Introduction — Hiểu NestJS trước khi viết code

> [!abstract] Mục tiêu của bài
> Hiểu NestJS dùng để làm gì, các công nghệ xung quanh nó giữ vai trò gì, vì sao code được chia thành nhiều thành phần và cách đọc bước đầu những dòng có dấu `@`.
>
> Đây là bài mở đầu. Phần đọc code bên dưới là ví dụ dẫn nhập; mỗi thành phần sẽ có bài riêng trong chương Overview.

## 1. NestJS dùng để làm gì?

**NestJS là framework để xây dựng ứng dụng phía máy chủ, chạy trên Node.js và hỗ trợ TypeScript.**[^intro]

Ví dụ bạn làm chức năng cập nhật hồ sơ:

1. Người dùng nhập họ tên trên giao diện.
2. Giao diện gửi yêu cầu cập nhật đến backend.
3. Backend kiểm tra người gửi, dữ liệu và điều kiện cập nhật.
4. Backend lưu thay đổi rồi trả kết quả.

NestJS giúp bạn xây dựng phần backend này.

**Framework** có thể hiểu là bộ công cụ cùng một cách tổ chức ứng dụng. Bạn viết phần xử lý của mình; framework kết nối các thành phần và gọi chúng khi cần.

Một API có thể là `PATCH /user/me`: `PATCH` là phương thức HTTP, `/user/me` là đường dẫn. Backend dùng hai thông tin này để chọn phần xử lý phù hợp.[^controllers]

## 2. Phân biệt Node.js, TypeScript, NestJS và Express

Những cái tên này giữ các vai trò khác nhau:

| Thành phần | Vai trò | Hiểu bằng câu đơn giản |
| --- | --- | --- |
| JavaScript | Ngôn ngữ lập trình | Ngôn ngữ dùng để viết logic. |
| TypeScript | Bổ sung hệ thống kiểu cho JavaScript | Giúp kiểm tra nhiều lỗi về kiểu trước khi chạy. |
| Node.js | Môi trường chạy JavaScript bên ngoài trình duyệt | Cho code backend một nơi để chạy. |
| Express / Fastify | Framework HTTP bên dưới Nest | Đảm nhiệm cơ chế HTTP qua adapter tương ứng. |
| NestJS | Framework tổ chức và xây dựng ứng dụng | Kết nối controller, provider, module và các thành phần khác. |

Node.js là runtime, tức môi trường thực thi code.[^node] TypeScript giúp kiểm tra kiểu tĩnh.[^ts] Với ứng dụng HTTP, Nest mặc định dùng Express và có thể chuyển sang Fastify.[^intro]

Ví dụ kiểu dữ liệu:

```ts
const fullName: string = 'Tinh';
```

`string` cho TypeScript biết biến này được khai báo để chứa chuỗi. Nếu gán số vào biến đó, trình kiểm tra kiểu sẽ báo lỗi.

> [!important] Kiểu TypeScript và dữ liệu HTTP
> Khai báo `request: UpdateProfileDto` giúp mô tả kiểu trong code. Để kiểm tra dữ liệu người dùng gửi khi ứng dụng chạy, cần cấu hình validation, chẳng hạn `ValidationPipe`.[^validation]

## 3. Vì sao NestJS quan tâm đến cách tổ chức code?

Tưởng tượng chức năng cập nhật hồ sơ phải đọc request, kiểm tra đăng nhập, kiểm tra họ tên, lưu database và định dạng kết quả. Khi sửa quy tắc họ tên, bạn muốn biết ngay phần nào chịu trách nhiệm cho quy tắc đó.

Nest cung cấp một cấu trúc ứng dụng hướng đến khả năng kiểm thử, bảo trì và mở rộng.[^intro] Các vai trò thường gặp gồm:

| Thành phần | Việc nó phụ trách | Ví dụ |
| --- | --- | --- |
| Controller | Nhận request và trả response | Nhận yêu cầu cập nhật hồ sơ. |
| Service | Thực hiện xử lý được controller giao cho | Áp dụng quy tắc cập nhật hồ sơ. |
| Provider | Thành phần Nest có thể quản lý và cung cấp cho nơi cần | Service thường là một provider. |
| Module | Khai báo và gom các thành phần liên quan | Gom controller và service của chức năng người dùng. |

Controller giao xử lý phức tạp cho provider.[^providers] Module cho Nest biết các thành phần nào thuộc về nó và liên hệ với module khác ra sao.[^modules]

**Vì sao cần chia như vậy?** Khi đọc code, bạn có nơi để tìm từng trách nhiệm. Khi kiểm thử quy tắc cập nhật, bạn có thể tập trung vào phần xử lý quy tắc; khi đổi URL, bạn biết cần xem controller.

Trang Introduction cũng nhắc đến ba cách lập trình:

| Tên | Ý nghĩa bước đầu |
| --- | --- |
| OOP — Object-oriented programming | Tổ chức code bằng đối tượng và class. |
| FP — Functional programming | Tổ chức xử lý bằng các hàm. |
| FRP — Functional reactive programming | Làm việc với các luồng dữ liệu hoặc sự kiện thay đổi theo thời gian. |

Bạn sẽ gặp chúng qua ví dụ thực tế trong các bài tiếp theo.

## 4. Nhìn dấu `@` trong code như thế nào?

Trong những ví dụ NestJS này, cú pháp bắt đầu bằng `@` là **decorator**. Decorator gắn thông tin hoặc hành vi vào một khai báo như class, method hay tham số.[^decorators]

Nhiều decorator của Nest ghi **metadata** — thông tin bổ sung để Nest biết cách sử dụng đoạn code đó. Ví dụ, class nào là controller và method nào xử lý một đường dẫn.

### 4.1. Vì sao có cái nằm trên class, có cái nằm trong dấu `()`?

**Vị trí cho biết decorator đang gắn với thành phần nào.**

| Vị trí | Nó gắn với gì? | Ví dụ |
| --- | --- | --- |
| Trên khai báo class | Class đó | `@Controller('user')` |
| Trên khai báo method | Method đó | `@Patch('me')` |
| Trước một tham số trong dấu `()` của method | Tham số đó | `@Body() request: UpdateProfileDto` |

Đây là vị trí khai báo method và tham số. Khi có request, Nest lấy dữ liệu tương ứng rồi truyền vào method.[^custom]

### 4.2. Đọc nhanh một đoạn trong ví dụ của bạn

Đây là một phần controller; các tên riêng cần được định nghĩa và import trong dự án:

```ts
@Controller('user')
@UseGuards(AccessTokenGuard)
@UseFilters(UsersExceptionFilter)
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Patch('me')
  async updateProfile(
    @CurrentUserId() userId: string,
    @Body() request: UpdateProfileDto,
  ): Promise<UserProfileResponse> {
    const result = await this.usersService.updateProfile(userId, {
      fullName: request.fullName,
      avatarUrl: request.avatarUrl,
    });

    return toUserProfileResponse(result);
  }
}
```

| Dòng code | Đọc bằng lời |
| --- | --- |
| `@Controller('user')` | Đánh dấu class là controller, dùng tiền tố đường dẫn `/user`. |
| `@Patch('me')` | Method bên dưới xử lý `PATCH /user/me`, nếu không có tiền tố toàn cục hay cấu hình đường dẫn bổ sung. |
| `@UseGuards(AccessTokenGuard)` | Gắn guard cho controller. Guard quyết định request có được đi tiếp vào handler hay không. |
| `@UseFilters(UsersExceptionFilter)` | Gắn exception filter cho controller để xử lý những exception chưa được bắt mà filter đó phù hợp để xử lý. |
| `@CurrentUserId() userId: string` | Gắn decorator vào tham số `userId`; cách lấy ID phụ thuộc phần định nghĩa decorator. |
| `@Body() request: UpdateProfileDto` | Lấy body của request làm giá trị cho tham số `request`. |

Guard chạy trước handler.[^guards] Exception filter tham gia khi xảy ra exception chưa được xử lý; nó có thể quyết định cách trả lỗi.[^filters]

> [!note] Các tên riêng cần xem định nghĩa
> `CurrentUserId`, `CurrentSessionId`, `AccessTokenGuard` và `UsersExceptionFilter` là tên do dự án hoặc thư viện lựa chọn.
>
> Theo tên gọi, hai decorator `Current...` có thể dùng để lấy ID người dùng và phiên hiện tại. Muốn biết chúng đọc từ đâu, phải xem code định nghĩa; tên gọi chưa chứng minh được cách hoạt động.

### 4.3. Vì sao service nằm trong constructor?

```ts
constructor(private readonly usersService: UsersService) {}
```

Controller khai báo rằng nó cần một `UsersService`. Khi dependency đã được đăng ký đúng, Nest cung cấp instance tương ứng. Cơ chế này gọi là **dependency injection**, tức cung cấp thành phần phụ thuộc cho nơi cần dùng.[^providers]

`private readonly` đồng thời khai báo thuộc tính của class theo cú pháp TypeScript: `private` giới hạn truy cập ở mức kiểm tra kiểu, `readonly` hạn chế gán lại thuộc tính sau khởi tạo. Nó không làm toàn bộ object service trở thành bất biến.

Trong thân method, `this.usersService` là thuộc tính ấy. `await` chờ lời gọi bất đồng bộ hoàn tất. `Promise<UserProfileResponse>` mô tả kiểu kết quả của method bất đồng bộ.

`toUserProfileResponse(result)` là hàm riêng. Theo tên, nó có thể chuyển kết quả sang cấu trúc response; cần xem định nghĩa để biết chính xác.

### 4.4. `@HttpCode()` để làm gì?

```ts
@Post('me/change-password')
@HttpCode(HttpStatus.NO_CONTENT)
```

`@HttpCode()` đặt **mã trạng thái HTTP cho response thành công của handler**. `HttpStatus.NO_CONTENT` là giá trị `204`: thành công và không có nội dung response body.

Trong cơ chế response tiêu chuẩn của Nest, mặc định là `200`, riêng `POST` là `201`; decorator này thay đổi mặc định đó. Exception được xử lý theo cơ chế trả lỗi.[^controllers]

## 5. Tạo project: hiểu từng lệnh

### 5.1. Kiểm tra công cụ

```bash
node -v
npm -v
```

Hai lệnh in phiên bản Node.js và npm. npm là công cụ quản lý package — những gói code mà dự án sử dụng.

> [!info] Phiên bản đã đối chiếu ngày 10/10/2026
> Theo trang First steps, các lệnh tạo project của Nest CLI yêu cầu Node.js `22.22.3+`, `24.15+` hoặc `26+` theo dòng phiên bản tương ứng. Chọn bản LTS đáp ứng điều kiện này và kiểm tra lại tài liệu khi cài đặt về sau.[^first]

### 5.2. Cài Nest CLI

```bash
npm i -g @nestjs/cli
```

| Phần lệnh | Ý nghĩa |
| --- | --- |
| `npm` | Chạy công cụ npm. |
| `i` | Viết tắt của `install`, yêu cầu cài package. |
| `-g` | Cài theo chế độ global, để dùng công cụ dòng lệnh ngoài một project cụ thể. |
| `@nestjs/cli` | Tên package chứa Nest CLI. |

**CLI** là công cụ dùng qua dòng lệnh. Nest CLI giúp tạo project và các thành phần theo cấu trúc có sẵn.[^cli]

> [!tip] Dấu `@` còn tùy ngữ cảnh
> Trong tên package `@nestjs/cli`, `@nestjs` là scope của package. Còn `@Controller()` trong code là cú pháp decorator.

Chế độ global áp dụng cho CLI; các dependency của từng project vẫn được cài riêng.[^npm-install]

### 5.3. Tạo project

```bash
nest new nest-learning
```

`nest` gọi CLI, `new` yêu cầu tạo project, `nest-learning` là tên thư mục project. CLI tạo các file khởi đầu và cài dependency.

Nếu được hỏi trình quản lý package, có thể chọn `npm` để theo các lệnh trong ghi chú này. Các lựa chọn bổ sung có thể khác theo phiên bản CLI.

### 5.4. Vào thư mục rồi chạy ứng dụng

```bash
cd nest-learning
npm run start:dev
```

`cd` chuyển thư mục làm việc. Cần vào thư mục project trước để chạy script của project đó.

`npm run start:dev` yêu cầu npm chạy script tên `start:dev` trong `package.json`.[^npm-run] Với project mẫu, chế độ này theo dõi thay đổi file và khởi động lại server.[^first]

Ứng dụng mẫu thường được kiểm tra tại `http://localhost:3000`, nếu chưa đổi cấu hình cổng.

## 6. Các file khởi đầu giữ vai trò gì?

| File trong `src/` | Vai trò bước đầu |
| --- | --- |
| `main.ts` | Điểm khởi động: tạo ứng dụng và bắt đầu lắng nghe request. |
| `app.module.ts` | Module gốc của ứng dụng. |
| `app.controller.ts` | Controller mẫu nhận request. |
| `app.service.ts` | Service mẫu cung cấp phần xử lý. |
| `app.controller.spec.ts` | File kiểm thử controller. |

Đây là các file cốt lõi được giới thiệu trong First steps; project của bạn có thể có thêm file tùy lựa chọn CLI.[^first]

> [!question]- Tự kiểm tra sau bài Introduction
> 1. Vì sao backend cần Node.js?
> 2. NestJS giúp tổ chức ứng dụng bằng những thành phần nào?
> 3. Vì sao `@Body()` nằm trước tham số `request`?
> 4. `@HttpCode(204)` diễn đạt điều gì về response?
> 5. `npm run start:dev` lấy tên script từ file nào?

**Bài tiếp theo:** [[01-first-steps|First steps — đọc từng dòng main.ts và cách ứng dụng khởi động]].

## Nguồn để đọc thêm

Các ví dụ và phần giải thích được viết lại để học; phần Introduction chính thức nằm ở nguồn đầu tiên. Các chương khác được dùng cho phần dẫn nhập về code.

[^intro]: [NestJS — Introduction](https://docs.nestjs.com/)
[^node]: [Node.js — Introduction to Node.js](https://nodejs.org/learn/getting-started/introduction-to-nodejs)
[^ts]: [TypeScript — TypeScript for the New Programmer](https://www.typescriptlang.org/docs/handbook/typescript-from-scratch.html)
[^controllers]: [NestJS — Controllers](https://docs.nestjs.com/controllers)
[^providers]: [NestJS — Providers](https://docs.nestjs.com/providers)
[^modules]: [NestJS — Modules](https://docs.nestjs.com/modules)
[^decorators]: [TypeScript — Decorators](https://www.typescriptlang.org/docs/handbook/decorators.html)
[^custom]: [NestJS — Custom decorators](https://docs.nestjs.com/custom-decorators)
[^guards]: [NestJS — Guards](https://docs.nestjs.com/guards)
[^filters]: [NestJS — Exception filters](https://docs.nestjs.com/exception-filters)
[^validation]: [NestJS — Validation](https://docs.nestjs.com/techniques/validation)
[^first]: [NestJS — First steps](https://docs.nestjs.com/first-steps)
[^cli]: [NestJS — CLI overview](https://docs.nestjs.com/cli/overview)
[^npm-install]: [npm — install](https://docs.npmjs.com/cli/v11/commands/npm-install/)
[^npm-run]: [npm — run](https://docs.npmjs.com/cli/v11/commands/npm-run/)
