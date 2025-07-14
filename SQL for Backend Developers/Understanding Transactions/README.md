## **Giới thiệu**

Tính đến thời điểm này, bạn đã học khá nhiều về truy vấn và thay đổi dữ liệu trong cơ sở dữ liệu. Bạn cũng đã biết một số lý thuyết về **giao dịch** và vai trò của chúng trong việc đảm bảo **tính nhất quán và toàn vẹn** của dữ liệu.

Bây giờ, hãy áp dụng kiến thức đó vào thực tế: cách giao dịch được thực hiện, quản lý và kết thúc.

### 💡 Tại sao giao dịch lại quan trọng?

Hãy tưởng tượng tình huống: bạn đang xây dựng một ứng dụng ngân hàng đơn giản. Khách hàng muốn **chuyển tiền giữa hai tài khoản**. Đầu tiên, bạn cần trừ tiền khỏi tài khoản người gửi, sau đó cộng tiền vào tài khoản người nhận.

Nếu bạn chỉ thực hiện `UPDATE` để trừ tiền mà không đảm bảo tài khoản người gửi còn đủ tiền, hệ thống có thể gặp lỗi. Nếu lỗi xảy ra sau khi đã trừ tiền nhưng **chưa cộng vào tài khoản người nhận**, hệ thống sẽ không nhất quán.

Khi đó, bạn cần một cơ chế để **đảm bảo cả hai thao tác xảy ra trọn vẹn hoặc không gì cả**. Đó chính là vai trò của **transaction** (giao dịch).

---

## **SQL Transaction là gì?**

Giao dịch là một **chuỗi các thao tác SQL** (thêm, sửa, xóa dữ liệu) được thực hiện như một đơn vị thống nhất. Có hai loại giao dịch:

* **Ngầm định (Implicit):** xảy ra tự động với các truy vấn đơn như `INSERT`, `UPDATE`, `DELETE`.
* **Tường minh (Explicit):** dùng các lệnh như `START TRANSACTION`, `SAVEPOINT`, `COMMIT`, `ROLLBACK`.

---

### ✅ **Hoàn thành giao dịch thành công**

Giả sử bạn có bảng `employees` như sau:

| id | surname | name   | age |
| -- | ------- | ------ | --- |
| 1  | Johnson | Ethan  | 21  |
| 2  | Brown   | Kevin  | 25  |
| 3  | Hall    | Justin | 25  |

Tạo bảng như sau:

```sql
CREATE TABLE employees (
  id INT PRIMARY KEY,
  surname VARCHAR(60),
  name VARCHAR(50),
  age INT
);
```

**Nhiệm vụ**: cập nhật tuổi của nhân viên tên "Justin":

```sql
START TRANSACTION;
UPDATE employees
SET age = 18
WHERE name = 'Justin';
COMMIT;
```

Sau khi giao dịch hoàn tất, dữ liệu sẽ là:

| id | surname | name   | age |
| -- | ------- | ------ | --- |
| 1  | Johnson | Ethan  | 21  |
| 2  | Brown   | Kevin  | 25  |
| 3  | Hall    | Justin | 18  |

---

### ❌ **Hủy giao dịch**

Giờ hãy thử xóa nhân viên có họ là "Brown", nhưng sau đó **hủy giao dịch**:

```sql
START TRANSACTION;
DELETE FROM employees
WHERE surname = 'Brown';
ROLLBACK TRANSACTION;
```

Bảng kết quả:

| id | surname | name   | age |
| -- | ------- | ------ | --- |
| 1  | Johnson | Ethan  | 21  |
| 2  | Brown   | Kevin  | 25  |
| 3  | Hall    | Justin | 18  |

💡 Như bạn thấy, **không có thay đổi nào xảy ra** vì chúng ta đã rollback (quay lui) giao dịch.

---

### 🧩 **Điểm lưu giao dịch – Savepoint**

Bạn có thể tạo các điểm lưu để quay lui một phần giao dịch. Ví dụ:

```sql
START TRANSACTION;

SAVEPOINT sp1;
INSERT INTO employees (id, surname, name, age)
VALUES (4, 'Smith', 'David', 18);

SAVEPOINT sp2;
INSERT INTO employees (id, surname, name, age)
VALUES (5, 'Williams', 'Robert', 24);

ROLLBACK TO SAVEPOINT sp2;

INSERT INTO employees (id, surname, name, age)
VALUES (6, 'Miller', 'Zara', 20);

COMMIT;
```

✅ Kết quả cuối cùng:

| id | surname | name   | age |
| -- | ------- | ------ | --- |
| 1  | Johnson | Ethan  | 21  |
| 2  | Brown   | Kevin  | 25  |
| 3  | Hall    | Justin | 18  |
| 4  | Smith   | David  | 18  |

👉 Nhân viên “Smith” được thêm vào, nhưng các lệnh sau điểm lưu `sp2` đã bị rollback (hủy bỏ). Các thay đổi diễn ra chính xác như kỳ vọng.

---

## **Kết luận**

* **Giao dịch** giúp kiểm soát và bảo vệ dữ liệu trong quá trình thay đổi.
* Có hai loại: **ngầm định** và **tường minh**.
* Giao dịch tường minh thường bao gồm:

  ```sql
  START TRANSACTION;
  SAVEPOINT [tên điểm lưu];
  -- một số lệnh SQL
  ROLLBACK TO SAVEPOINT [tên];
  ROLLBACK; -- hoặc COMMIT;
  ```

---

🎉 **Chúc mừng!** Giờ bạn đã hiểu rõ hơn về cách viết, thực hiện và quản lý giao dịch SQL rồi.
