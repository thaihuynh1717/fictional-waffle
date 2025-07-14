## **Phép toán tập hợp trong SQL (Set Operations)**

Đôi khi bạn cần kết hợp kết quả từ nhiều câu truy vấn SELECT có cùng cấu trúc (tức là cùng số và kiểu cột), và trả về thành một kết quả duy nhất. Để làm được điều đó, bạn sẽ sử dụng **các phép toán tập hợp**.

Các phép toán này gồm có:

* `UNION`
* `UNION ALL`
* `INTERSECT`
* `EXCEPT` (hoặc `MINUS`)

> 🔹 Tất cả các phép toán này yêu cầu các câu truy vấn con phải có:
>
> * Cùng số lượng cột
> * Kiểu dữ liệu tương ứng
> * Các cột ở cùng vị trí có kiểu dữ liệu tương thích

---

## **UNION**

Toán tử `UNION` kết hợp các dòng kết quả của hai hoặc nhiều câu truy vấn `SELECT`, đồng thời **loại bỏ các giá trị trùng lặp**.

### Ví dụ:

Chúng ta có 2 bảng:

**teachers**:

| name           | subject   |
| -------------- | --------- |
| Ginevra Holmes | Geography |
| Carl Robinson  | Math      |
| Tamara Fetch   | IT        |
| Robert Borck   | English   |

**administrative\_staff**:

| position     | name         |
| ------------ | ------------ |
| coordinator  | Tamara James |
| deputy head  | Tamara Fetch |
| head teacher | Ann Brown    |

### Truy vấn:

```sql
SELECT name FROM teachers
UNION
SELECT name FROM administrative_staff;
```

### Kết quả:

| name           |
| -------------- |
| Ginevra Holmes |
| Carl Robinson  |
| Tamara Fetch   |
| Robert Borck   |
| Tamara James   |
| Ann Brown      |

> ✅ `Tamara Fetch` chỉ xuất hiện một lần vì `UNION` loại bỏ trùng lặp.

---

## **UNION ALL**

Toán tử `UNION ALL` tương tự `UNION`, **nhưng giữ lại tất cả các giá trị trùng lặp**.

```sql
SELECT name FROM teachers
UNION ALL
SELECT name FROM administrative_staff;
```

### Kết quả:

| name           |
| -------------- |
| Ginevra Holmes |
| Carl Robinson  |
| Tamara Fetch   |
| Robert Borck   |
| Tamara James   |
| Tamara Fetch   |
| Ann Brown      |

> 🔸 `Tamara Fetch` xuất hiện 2 lần vì có trong cả 2 bảng.

---

## **INTERSECT**

Toán tử `INTERSECT` trả về các dòng **xuất hiện ở cả hai tập kết quả** (không trùng lặp).

```sql
SELECT name FROM teachers
INTERSECT
SELECT name FROM administrative_staff;
```

### Kết quả:

| name         |
| ------------ |
| Tamara Fetch |

> ⚠️ **Lưu ý:** MySQL KHÔNG hỗ trợ `INTERSECT`.

---

## **EXCEPT** hoặc **MINUS**

Toán tử `EXCEPT` (hoặc `MINUS`) trả về các dòng **chỉ có trong truy vấn đầu tiên mà không có trong truy vấn thứ hai**.

```sql
SELECT name FROM teachers
EXCEPT
SELECT name FROM administrative_staff;
```

### Kết quả:

| name           |
| -------------- |
| Ginevra Holmes |
| Carl Robinson  |
| Robert Borck   |

> Trong một số hệ quản trị như Oracle, dùng `MINUS` thay cho `EXCEPT`.

Ngược lại, nếu muốn lấy **những người trong bảng nhân sự không phải là giáo viên**:

```sql
SELECT name FROM administrative_staff
EXCEPT
SELECT name FROM teachers;
```

### Kết quả:

| name         |
| ------------ |
| Tamara James |
| Ann Brown    |

---

## **Tổng kết**

Bạn có thể dùng mẫu sau để viết câu truy vấn với các phép toán tập hợp:

```sql
SELECT column_1, column_2
FROM (
    SELECT column_1, column_2 FROM table_1
    WHERE điều_kiện_1
    [SET_OPERATOR]
    SELECT column_1, column_2 FROM table_2
    WHERE điều_kiện_2
) AS result;
```

📌 **Ghi chú:**

* `SET_OPERATOR` là: `UNION`, `UNION ALL`, `INTERSECT`, hoặc `EXCEPT`
* MySQL **chỉ hỗ trợ** `UNION` và `UNION ALL`.
  `INTERSECT` và `EXCEPT` có thể không khả dụng tùy vào hệ quản trị cơ sở dữ liệu bạn dùng.

---

✅ Giờ bạn đã hiểu rõ cách kết hợp kết quả nhiều truy vấn với các phép toán tập hợp trong SQL!
