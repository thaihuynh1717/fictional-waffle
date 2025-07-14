
### Hiểu về Chỉ mục Cơ sở Dữ liệu - Được giải thích bằng ví dụ

Là một quy tắc chung, thời gian truy vấn tăng lên khi dữ liệu tăng. Hầu hết các hệ quản trị cơ sở dữ liệu hiện đại cung cấp một tính năng đặc biệt để xử lý vấn đề này – **chỉ mục**. Chỉ mục giúp tăng tốc đáng kể các thao tác tìm kiếm trên các bảng cơ sở dữ liệu.

Trong bài viết này, bạn sẽ tìm hiểu về các loại chỉ mục phổ biến trong cơ sở dữ liệu và cách chúng được triển khai.

---

### Một phép ẩn dụ đời thực

Trước khi bắt đầu với chỉ mục cơ sở dữ liệu, hãy xem một phép ẩn dụ. Hãy tưởng tượng một thư viện với hàng nghìn cuốn sách. Chỉ mục là danh sách các cuốn sách được sắp xếp theo các tiêu chí khác nhau (ví dụ: tên tác giả, tiêu đề, năm phát hành). Nếu bạn muốn tìm một cuốn sách cụ thể, bạn sẽ tìm trong chỉ mục chứ không phải xem từng cuốn một.

Tương tự như vậy, chỉ mục cơ sở dữ liệu lưu trữ dữ liệu theo một cách dễ tìm kiếm hơn. Các chỉ mục giúp truy xuất nhanh chóng bằng cách duyệt qua một tập hợp con của dữ liệu thay vì toàn bộ bảng.

---

### Chỉ mục trong cơ sở dữ liệu

Theo mặc định, các hệ quản trị cơ sở dữ liệu không tạo chỉ mục trừ khi bạn chỉ rõ chúng. Chỉ mục là các cấu trúc dữ liệu đặc biệt giúp tìm kiếm hiệu quả hơn. Tuy nhiên, có sự đánh đổi – chúng chiếm dung lượng bộ nhớ bổ sung và làm chậm các thao tác ghi như `INSERT`, `UPDATE`, hoặc `DELETE`.

> 📌 **Lưu ý:** Càng nhiều chỉ mục, càng tốn tài nguyên. PostgreSQL, MySQL, SQLite và các hệ quản trị khác đều cho phép tạo chỉ mục thủ công, vì vậy bạn nên chỉ tạo khi cần thiết, dựa trên truy vấn thực tế.

---

### Phân loại chỉ mục

Dựa theo PostgreSQL và MySQL (các hệ thống phổ biến), có nhiều loại chỉ mục khác nhau. Dưới đây là một số loại chỉ mục chính được phân loại theo:

#### ➤ Chỉ mục đơn giản

Chỉ mục đơn giản chỉ bao gồm một cột. Ví dụ: `CREATE INDEX idx_name ON users(name);` sẽ tạo chỉ mục trên cột `name`.

#### ➤ Chỉ mục phức hợp

Chứa nhiều hơn một cột (ví dụ: `CREATE INDEX idx_comp ON users(first_name, last_name);`) và cho phép các truy vấn theo nhiều cột được tối ưu hóa tốt hơn.

#### ➤ Chỉ mục cụm và không cụm

* **Cụm (clustered index):** Sắp xếp dữ liệu vật lý trên đĩa theo thứ tự của chỉ mục. Mỗi bảng chỉ có một chỉ mục dạng cụm.
* **Không cụm (non-clustered):** Lưu trữ con trỏ tới vị trí dữ liệu thực tế.

---

### Phân loại theo cấu trúc dữ liệu

Theo cấu trúc dữ liệu, các chỉ mục được chia thành bốn loại phổ biến:

* **B-Tree (Balanced Tree)**
* **Hash**
* **Bitmap**
* **Spatial (Không gian)**

Chúng ta sẽ tìm hiểu chi tiết hai loại quan trọng nhất là **B-Tree** và **Hash**.

---

### B-Tree Index

Đây là loại phổ biến nhất. Khi bạn không chỉ rõ loại chỉ mục, hệ quản trị mặc định sẽ tạo chỉ mục dạng B-Tree.

#### ➤ Khi nào dùng B-Tree?

* Khi cần sắp xếp dữ liệu (`ORDER BY`)
* Khi truy vấn với so sánh (`=`, `<`, `>`, `BETWEEN`, `IN`)
* Khi lọc dữ liệu theo khoảng

> B-Tree là cấu trúc cây nhị phân cân bằng. Nó lưu trữ giá trị theo thứ tự và duy trì thứ tự đó. Do vậy, tìm kiếm trên B-Tree rất hiệu quả, thường là O(log n).

---

### Hash Index

Chỉ mục dạng Hash dựa trên hàm băm. Nó ánh xạ một giá trị đầu vào tới một giá trị băm cố định. Được sử dụng chủ yếu cho các truy vấn có điều kiện bằng (`=`).

#### Ưu điểm:

* Truy vấn bằng `=` nhanh hơn B-Tree
* Ít bộ nhớ hơn
* Tránh việc phải duyệt nhiều node như B-Tree

#### Nhược điểm:

* Không hỗ trợ so sánh `<`, `>`, `BETWEEN`, `ORDER BY`
* Không hỗ trợ tìm kiếm theo khoảng
* Ít phổ biến hơn trong các hệ quản trị

---

### Các loại chỉ mục khác

* **Bitmap Index:** Tốt cho các cột có số lượng giá trị riêng biệt thấp (nhị phân, boolean).
* **Giản đồ đảo (inverted index):** Dùng trong hệ thống tìm kiếm văn bản.
* **Chỉ mục không gian:** Dùng cho dữ liệu địa lý.

---

### Kết luận

Chỉ mục trong cơ sở dữ liệu rất quan trọng để tăng hiệu suất truy vấn. Dưới đây là tóm tắt:

* **B-Tree:** Đa năng nhất, hỗ trợ nhiều loại truy vấn.
* **Hash:** Hiệu quả với truy vấn `=`, nhưng không hỗ trợ các phép so sánh khác.
* **Bitmap & Inverted:** Tối ưu cho dữ liệu đặc biệt như boolean hoặc văn bản.

Việc chọn đúng chỉ mục phụ thuộc vào loại dữ liệu và loại truy vấn bạn thực hiện thường xuyên.

---

## **Phân tích các truy vấn SQL – Giải thích với EXPLAIN và các chỉ số**

Khi tối ưu hóa hiệu suất truy vấn, một bước quan trọng là **phân tích truy vấn**. Việc hiểu được cách truy vấn được thực thi giúp bạn điều chỉnh để cải thiện hiệu suất. PostgreSQL cung cấp từ khóa `EXPLAIN` để kiểm tra kế hoạch thực thi của truy vấn.

---

### **Phân tích cơ sở dữ liệu mẫu**

Ta sẽ sử dụng một cơ sở dữ liệu mẫu gồm hai bảng: `employees` và `salary`, trong đó `employees` chứa thông tin nhân viên và `salary` chứa mức lương ứng với thời gian.

```sql
CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  department VARCHAR(50) NOT NULL,
  hire_date DATE NOT NULL
);

CREATE TABLE salary (
  id SERIAL PRIMARY KEY,
  emp_id INTEGER NOT NULL REFERENCES employees(id),
  amount NUMERIC NOT NULL,
  from_date DATE NOT NULL,
  to_date DATE,
  CONSTRAINT unique_salary UNIQUE(emp_id, from_date),
  FOREIGN KEY (emp_id) REFERENCES employees(id)
);
```

---

### **Truy vấn mẫu**

Giả sử bạn muốn tìm tên nhân viên và lương hiện tại:

```sql
SELECT name, amount
FROM employees e
JOIN salary s ON e.id = s.emp_id
WHERE s.to_date IS NULL;
```

---

### **Từ khóa EXPLAIN**

Từ khóa `EXPLAIN` cho phép bạn xem **kế hoạch thực thi truy vấn**. Nó cho biết cách PostgreSQL dự định truy xuất dữ liệu – có sử dụng chỉ mục không, quét toàn bộ bảng hay join như thế nào.

```sql
EXPLAIN ANALYZE
SELECT name, salary
FROM employees e
JOIN salary s ON e.id = s.emp_id
WHERE s.to_date IS NULL;
```

Kết quả mẫu:

```
Nested Loop (cost=0.00..12.75 rows=5 width=32)
  -> Seq Scan on employees e  (cost=0.00..1.05 rows=5 width=16)
  -> Index Scan using salary_emp_id_idx on salary s  (cost=0.00..2.50 rows=1 width=16)
       Index Cond: (emp_id = e.id)
       Filter: (to_date IS NULL)
```

---

### **Chi tiết về kế hoạch**

* **Seq Scan**: Quét tuần tự toàn bộ bảng (không dùng chỉ mục).
* **Index Scan**: Quét bằng chỉ mục đã có.
* **Filter**: Điều kiện WHERE được áp dụng sau khi truy xuất.
* **Cost**: Dự đoán chi phí của truy vấn.
* **Rows**: Dự đoán số dòng kết quả.

Những thông tin này giúp bạn xác định phần nào của truy vấn cần tối ưu hơn.

---

### **Dùng kế hoạch để tối ưu truy vấn**

Ví dụ, bạn tạo một chỉ mục để tối ưu hóa điều kiện `to_date IS NULL`:

```sql
CREATE INDEX idx_salary_to_date ON salary(to_date);
```

Sau khi tạo chỉ mục, chạy lại `EXPLAIN` bạn sẽ thấy kế hoạch thay đổi:

```
Bitmap Heap Scan on salary
  -> Bitmap Index Scan on idx_salary_to_date
       Index Cond: (to_date IS NULL)
```

Khi đó PostgreSQL sử dụng chỉ mục mới thay vì quét toàn bảng.

---

### **So sánh kết quả dự đoán và thực tế**

Sử dụng `EXPLAIN ANALYZE` bạn sẽ thấy số hàng dự đoán và số hàng thực tế. Điều này giúp đánh giá độ chính xác của thống kê dữ liệu:

```sql
EXPLAIN ANALYZE
SELECT name, amount
FROM employees e
JOIN salary s ON e.id = s.emp_id
WHERE s.to_date IS NULL;
```

Kết quả:

```
Rows Removed by Filter: 20
Actual Rows: 5
```

Nếu dự đoán khác xa thực tế, bạn nên cập nhật thống kê:

```sql
ANALYZE;
```

---

### **Kết luận**

Phân tích truy vấn là một kỹ năng quan trọng giúp cải thiện hiệu suất. Bằng cách dùng `EXPLAIN`, `ANALYZE`, và thêm chỉ mục phù hợp, bạn có thể tối ưu hóa đáng kể các truy vấn của mình. PostgreSQL cung cấp nhiều công cụ để bạn kiểm tra, đánh giá và điều chỉnh hiệu quả thực thi truy vấn.

---

### **Ràng buộc trong SQL**

Mỗi cột trong bảng đều có một kiểu dữ liệu cụ thể, vì vậy không thể chèn văn bản vào một cột kiểu INT hoặc số thập phân vào một cột kiểu BOOLEAN trong dữ liệu SQL. Kiểu dữ liệu giúp đảm bảo tính chính xác của dữ liệu. Ngoài ra, đôi khi chúng ta muốn hạn chế dữ liệu được thêm vào, ví dụ: không được để trống, phải là duy nhất, lớn hơn một giá trị nhất định, v.v. Những ràng buộc này được gọi là **ràng buộc** (constraints).

---

### **Ví dụ**

Các ràng buộc phổ biến nhất là: `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, `PRIMARY KEY` và `FOREIGN KEY`. Trong phần này, chúng ta sẽ tìm hiểu về ràng buộc đầu tiên: **NOT NULL**.

Giả sử chúng ta có bảng đơn giản sau. Bảng này tạo ra một bảng `employees` có 3 cột: `personal_id`, `first_name`, `last_name`:

```sql
CREATE TABLE employees (
  personal_id INT,
  first_name VARCHAR(20),
  last_name VARCHAR(20)
);
```

---

### **Ràng buộc `NOT NULL`**

Ràng buộc `NOT NULL` đảm bảo rằng không được phép thêm giá trị `NULL` vào cột đó.

Ví dụ, thêm ràng buộc `NOT NULL` cho cột `first_name`:

```sql
ALTER TABLE employees
MODIFY first_name VARCHAR(20) NOT NULL;
```

Sau đó, nếu bạn cố gắng thêm một nhân viên mà không có tên, bạn sẽ gặp lỗi.

Bạn cũng có thể thêm `NOT NULL` khi tạo bảng:

```sql
CREATE TABLE employees (
  personal_id INT,
  first_name VARCHAR(20) NOT NULL,
  last_name VARCHAR(20)
);
```

---

### **Ràng buộc `UNIQUE`**

Ràng buộc `UNIQUE` ngăn việc chèn các giá trị trùng lặp vào cột. Ví dụ, nếu `personal_id` cần là duy nhất:

```sql
CREATE TABLE employees (
  personal_id INT UNIQUE,
  first_name VARCHAR(20),
  last_name VARCHAR(20)
);
```

Thêm `UNIQUE` cho bảng đã có sẵn:

```sql
ALTER TABLE employees
ADD UNIQUE (personal_id);
```

Nếu cần chỉ định nhiều hơn một cột là duy nhất, bạn có thể viết:

```sql
CREATE TABLE employees (
  personal_id INT,
  first_name VARCHAR(20),
  last_name VARCHAR(20),
  CONSTRAINT un_id_name UNIQUE (personal_id, last_name)
);
```

Xóa ràng buộc UNIQUE:

```sql
ALTER TABLE employees
DROP INDEX un_id_name;
```

---

### **Ràng buộc `CHECK`**

Ràng buộc `CHECK` dùng để kiểm tra điều kiện logic. Ví dụ, chỉ cho phép tuổi nhân viên lớn hơn 16:

```sql
CREATE TABLE employees (
  personal_id INT,
  first_name VARCHAR(20),
  last_name VARCHAR(20),
  age INT CHECK (age > 16)
);
```

Hoặc thêm vào bảng có sẵn:

```sql
ALTER TABLE employees
ADD CHECK (age > 16);
```

Hoặc ràng buộc phức tạp hơn:

```sql
CREATE TABLE employees (
  ...
  CHECK (age > 16 AND personal_id > 0)
);
```

Bạn có thể xóa ràng buộc `CHECK`:

```sql
ALTER TABLE employees
DROP CHECK age;
```

---

### **Ràng buộc `DEFAULT`**

Ràng buộc `DEFAULT` đặt giá trị mặc định nếu không có giá trị nào được cung cấp.

```sql
CREATE TABLE employees (
  first_name VARCHAR(20) DEFAULT 'John',
  last_name VARCHAR(20) DEFAULT 'Doe'
);
```

Thêm ràng buộc `DEFAULT`:

```sql
ALTER TABLE employees
ALTER first_name SET DEFAULT 'John';
```

Xóa:

```sql
ALTER TABLE employees
ALTER first_name DROP DEFAULT;
```

---

### **Kết hợp nhiều ràng buộc**

Một cột có thể có nhiều ràng buộc. Ví dụ, kết hợp `NOT NULL`, `UNIQUE`, `DEFAULT`, và `CHECK`:

```sql
CREATE TABLE employees (
  personal_id INT NOT NULL UNIQUE,
  first_name VARCHAR(20) NOT NULL DEFAULT 'John',
  last_name VARCHAR(20) NOT NULL DEFAULT 'Doe',
  age INT DEFAULT 17,
  CHECK (age > 16)
);
```

---

### **Kết luận**

Giờ bạn đã biết cách tạo bảng đơn giản, thêm ràng buộc để đảm bảo dữ liệu hợp lệ và rõ ràng hơn khi tạo bảng, thêm ràng buộc vào bảng có sẵn và xóa ràng buộc nếu cần.

---

