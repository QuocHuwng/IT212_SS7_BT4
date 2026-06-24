
# BÀI 4: PHÂN TÍCH & LỰA CHỌN (PROMPT YÊU CẦU THÊM TEST CASE, KIỂM THỬ LOGIC)

## 1. Đáp án lựa chọn

**Phương án B**

---

# 2. Phân tích tại sao phương án B là tối ưu nhất

Phương án B là lựa chọn tốt nhất vì nó áp dụng đầy đủ các nguyên tắc viết Prompt cho kiểm thử phần mềm chuyên nghiệp:

* Xác định rõ vai trò (Role Prompting)
* Xác định rõ công nghệ kiểm thử
* Xác định phạm vi kiểm thử
* Xác định các Edge Cases bắt buộc
* Yêu cầu sử dụng Parameterized Test
* Định nghĩa rõ đầu ra mong muốn

Điều này giúp AI sinh ra bộ test có độ bao phủ (Coverage) cao và hạn chế bỏ sót lỗi.

---

## 2.1. Role Prompting

Prompt quy định:

```text
Hãy đóng vai trò là một Senior QA Engineer.
```

Điều này khiến AI tập trung vào:

* Tư duy kiểm thử
* Boundary Testing
* Edge Case Testing
* Test Coverage

thay vì chỉ viết vài test đơn giản.

---

## 2.2. Xác định công nghệ kiểm thử

Prompt chỉ rõ:

```text
JUnit 5
Assertions
Parameterized Test
```

Do đó AI sẽ sử dụng:

```java
@ParameterizedTest
@ValueSource
Assertions.assertTrue()
Assertions.assertFalse()
```

thay vì:

```java
main()
System.out.println()
```

hoặc các framework khác.

---

## 2.3. Bao phủ Edge Cases

Prompt quy định đầy đủ:

### Null

```java
null
```

### Chuỗi rỗng

```java
""
```

### Khoảng trắng

```java
" "
```

### Quá ngắn

```java
"Abc1@xy"
```

7 ký tự.

### Quá dài

21 ký tự.

### Thiếu chữ hoa

```java
password1@
```

### Thiếu chữ thường

```java
PASSWORD1@
```

### Thiếu số

```java
Password@
```

### Thiếu ký tự đặc biệt

```java
Password1
```

Nhờ đó khả năng bỏ sót lỗi gần như bằng 0.

---

## 2.4. Parameterized Testing

Prompt yêu cầu:

```java
@ParameterizedTest
```

Giúp:

* Giảm lặp code.
* Dễ mở rộng.
* Dễ bảo trì.

Ví dụ:

```java
@ParameterizedTest
@ValueSource(strings = {
    "Password1@",
    "Admin123#",
    "StrongPass9$"
})
```

Một test có thể kiểm tra nhiều dữ liệu.

---

# 3. Phân tích lý do loại trừ các phương án còn lại

## Phương án A

Prompt:

```text
Viết unit test cho PasswordValidator.
```

### Nhược điểm

Không chỉ rõ:

* Framework
* Parameterized Test
* Edge Cases
* Coverage mong muốn

AI có thể chỉ viết:

```java
assertTrue(
    validator.isValid("Password1@")
);
```

và:

```java
assertFalse(
    validator.isValid("abc")
);
```

Coverage rất thấp.

---

## Phương án C

Prompt:

```text
Viết lại regex ngắn hơn để khỏi viết test.
```

### Nhược điểm

Hoàn toàn sai tư duy kiểm thử.

Trong phát triển phần mềm:

```text
Viết code tốt
≠
Không cần test
```

Regex càng ngắn càng có nguy cơ:

* Sai logic
* Thiếu điều kiện
* Không phát hiện regression

Unit Test là bắt buộc.

---

# 4. Giả định lớp PasswordValidator

```java
public class PasswordValidator {

    public boolean isValid(String password) {

        if (password == null) {
            return false;
        }

        return password.matches(
            "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@#$%]).{8,20}$"
        );
    }
}
```

---

# 5. Mã nguồn JUnit 5 hoàn chỉnh

## PasswordValidatorTest.java

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

class PasswordValidatorTest {

    private final PasswordValidator validator =
            new PasswordValidator();

    @ParameterizedTest
    @ValueSource(strings = {
            "Password1@",
            "Admin123#",
            "StrongPass9$",
            "Welcome2024%",
            "JavaDev1@"
    })
    @DisplayName("Valid passwords")
    void shouldReturnTrueForValidPasswords(
            String password) {

        assertTrue(
                validator.isValid(password));
    }

    @ParameterizedTest
    @ValueSource(strings = {
            "",
            " ",
            "abcdefg",
            "Abc1@xy",
            "password1@",
            "PASSWORD1@",
            "Password@",
            "Password1",
            "pass word1@"
    })
    @DisplayName("Invalid passwords")
    void shouldReturnFalseForInvalidPasswords(
            String password) {

        assertFalse(
                validator.isValid(password));
    }

    @Test
    @DisplayName("Null password")
    void shouldReturnFalseWhenPasswordIsNull() {

        assertFalse(
                validator.isValid(null));
    }

    @Test
    @DisplayName("Password too long")
    void shouldReturnFalseWhenPasswordTooLong() {

        String password =
                "Password1234567890@AB";

        assertTrue(password.length() > 20);

        assertFalse(
                validator.isValid(password));
    }

    @Test
    @DisplayName("Exactly 8 characters")
    void shouldAcceptMinimumBoundary() {

        assertTrue(
                validator.isValid("Abc1@def"));
    }

    @Test
    @DisplayName("Exactly 20 characters")
    void shouldAcceptMaximumBoundary() {

        String password =
                "Abcdef12345@ABCDEfG";

        assertEquals(
                20,
                password.length());

        assertTrue(
                validator.isValid(password));
    }
}
```

---

# 6. Giải thích bộ Test Case

## Nhóm 1: Happy Path

Kiểm tra các mật khẩu hợp lệ:

```text
Password1@
Admin123#
StrongPass9$
```

Mục tiêu:

Đảm bảo hệ thống chấp nhận dữ liệu đúng.

---

## Nhóm 2: Edge Cases

Kiểm tra:

```text
null
""
" "
```

Mục tiêu:

Đảm bảo không phát sinh lỗi ngoài ý muốn.

---

## Nhóm 3: Boundary Testing

Kiểm tra:

```text
7 ký tự
8 ký tự
20 ký tự
21 ký tự
```

Mục tiêu:

Đảm bảo điều kiện độ dài được xử lý chính xác.

---

## Nhóm 4: Rule Validation

Thiếu:

* Chữ hoa
* Chữ thường
* Chữ số
* Ký tự đặc biệt

Mục tiêu:

Kiểm tra từng quy tắc độc lập.

---

# 7. Kết luận

Phương án B là lựa chọn tối ưu nhất vì xác định rõ vai trò kiểm thử, framework sử dụng, kỹ thuật Parameterized Test và toàn bộ các trường hợp biên cần bao phủ. Nhờ đó AI có thể sinh ra bộ JUnit 5 test case đầy đủ, dễ bảo trì và đạt độ bao phủ kiểm thử cao hơn rất nhiều so với các phương án còn lại.
