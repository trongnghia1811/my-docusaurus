---
slug: bai-viet-thu-2
title: CPython là gì?
authors: [admin]
tags: [docusaurus, blog, huong-dan, lap-trinh]
---

# Đây là bài blog thứ hai của mình viết bằng Docusaurus.

Trong bài viết này, mình sẽ chia sẻ về những gì mình biết khi tìm hiểu về CPython.

- CPython là gì?
- Hành trình từ dòng code xuống IDE diễn ra thế nào?
- Cách CPython quản lý bộ nhớ?

## 1. CPython là gì?

Trước khi đi vào chi tiết, chúng ta cần làm rõ: Python là ngôn ngữ (tập hợp các quy tắc ngữ pháp). Còn CPython là phần mềm thực thi các quy tắc đó (Implementation).

Khi ta tải Python từ trang chủ về tức là ta đang tải CPython. Nó được viết hoàn toàn bằng C. Nó vừa là Compiler (biên dịch), vừa là Interpreter (thông dịch).

## 2. Hành trình của Code: Pipeline từ A đến Z

Cho ví dụ sau:

```
a = 10
print(a + 5)
```

Để máy tính hiểu được, dòng code này phải trải qua một quá trình nghiêm ngặt gồm 4 giai đoạn:

### Giai đoạn 1: Lexing (Phân tích từ vựng)

CPython đọc file code của ta như một chuỗi văn bản vô nghĩa. Lexer sẽ băm nhỏ chuỗi đó thành các đơn vị có nghĩa gọi là Tokens.

```
a -> NAME
= -> OP
10 -> NUMBER
```

Lexer của Python rất đặc biệt: nó quan tâm đến cả khoảng trắng (indentation) để sinh ra các token INDENT và DEDENT, giúp định hình khối lệnh.

### Giai đoạn 2: Parsing & AST (Xây dựng cây cú pháp trừu tượng)

Từ những token rời rạc trên, Parser sẽ sắp xếp chúng lại theo ngữ pháp và dựng lên một Cây Cú pháp Trừu tượng (Abstract Syntax Tree - AST). Máy tính không đọc từ trái sang phải như người, nó nhìn cấu trúc cây để biết cái nào là cha, cái nào là con, cái nào ưu tiên thực hiện trước (ví dụ: nhân chia trước, cộng trừ sau).

### Giai đoạn 3: Compilation (Biên dịch sang Bytecode)

Đến đây CPython sẽ dịch AST sang một ngôn ngữ trung gian gọi là Bytecode.

- Bytecode không phải là Mã máy (Machine Code) để CPU chạy trực tiếp.

- Nó là tập lệnh dành riêng cho máy ảo Python.

- Đây là lý do ta thấy file .pyc trong thư mục **pycache**.

### Giai đoạn 4: Python Virtual Machine (PVM)

Đây là "hoạt động" chính. PVM là một vòng lặp vô tận viết bằng C. Nó hoạt động dựa trên cơ chế Stack (Ngăn xếp). Với lệnh a = 10:

- PVM đẩy số 10 vào Stack.

- PVM lấy số 10 ra và cất vào biến a.

PVM đọc từng dòng Bytecode, gọi các hàm C tương ứng, và cuối cùng gọi System Call xuống Hệ điều hành để thực thi phần cứng.

## 3. Cách CPython quản lý bộ nhớ?

CPython quản lí bộ nhớ cực kì tinh vi

### Mọi thứ đều là Object (PyObject)

Trong CPython, số 10 không chỉ là 4 bytes nhị phân. Nó là một struct (cấu trúc) trong C tên là PyObject. Nó chứa rất nhiều metadata:

- Nó thuộc kiểu gì (int, str)?
- Có bao nhiêu người đang dùng nó (Reference Count)?

### Pymalloc & Cơ chế cấp phát

CPython không gọi malloc (xin RAM từ OS) mỗi khi ta tạo một biến, vì việc đó rất chậm. Thay vào đó, nó dùng Pymalloc:

- Nó xin sẵn những vùng đất lớn (Arenas).
- Chia nhỏ thành các hồ (Pools).
- Khi ta cần, nó "phát" ngay một mảnh nhỏ. Đây là lý do tạo object nhỏ trong Python rất nhanh.

### Dọn rác (Garbage Collection)

Làm sao Python biết khi nào xóa biến để giải phóng RAM?

- Reference Counting (Chủ đạo): Cứ ai dùng biến thì đếm +1, không dùng nữa thì -1. Về 0 thì xóa ngay lập tức.
- Cyclic GC (Hỗ trợ): Xử lý các ca khó như "Vòng tham chiếu" (A trỏ B, B trỏ A) mà Ref Count bó tay.

---

##

Các bạn có thể tham khảo qua slide dưới đây để hiểu sâu hơn về CPython:

- 🔗 https://www.canva.com/design/DAG_3EC7Suw/1LjWwYMgO59FCa64VBrI3w/edit
  (Slide sẽ mở ở tab mới để xem đầy đủ và rõ ràng hơn)

---

## 4. Kết luận

Hành trình từ dòng code trên IDE đến khi OS thực thi là một chặng đường dài đi qua Lexer, Parser, Compiler và PVM. CPython không chỉ là một trình thông dịch, nó là một kiệt tác kỹ thuật kết hợp giữa sự linh hoạt của ngôn ngữ bậc cao và sự chặt chẽ của C.

Hy vọng bài viết này giúp bạn có cái nhìn sâu sắc hơn về công cụ mình đang dùng hàng ngày. Đừng chỉ code, hãy hiểu code chạy như thế nào!
