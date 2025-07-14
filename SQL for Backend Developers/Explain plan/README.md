
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


