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
