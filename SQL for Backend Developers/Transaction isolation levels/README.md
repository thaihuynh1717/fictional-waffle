## **Mức độ cô lập giao dịch (Transaction Isolation Levels)**

Để duy trì tính nhất quán trong cơ sở dữ liệu, một giao dịch cần tuân thủ các thuộc tính ACID. Hệ quản trị cơ sở dữ liệu (DBMS) đảm bảo điều này bằng cách sử dụng các cơ chế khóa đọc/ghi, tùy theo **mức độ cô lập giao dịch**.

Trong bài này, chúng ta sẽ tìm hiểu các mức độ cô lập và các lỗi có thể xảy ra khi nhiều giao dịch chạy đồng thời.

---

## **Mức độ cô lập giao dịch là gì?**

**Transaction Isolation** là mức độ kiểm soát dữ liệu nào có thể được “nhìn thấy” trong một giao dịch. Việc dữ liệu thay đổi đồng thời giữa các giao dịch có thể ảnh hưởng đến logic ứng dụng hoặc gây ra lỗi.

### 4 mức độ cô lập tiêu chuẩn:

* **Read Uncommitted**
* **Read Committed**
* **Repeatable Read**
* **Serializable**

Chúng xếp theo thứ tự từ ít nghiêm ngặt đến nghiêm ngặt nhất:

```
Read Uncommitted → Read Committed → Repeatable Read → Serializable
```

---

## 1. **Read Uncommitted** (Đọc dữ liệu chưa cam kết)

Cho phép giao dịch đọc dữ liệu **dù dữ liệu đó chưa được cam kết** (commit). Điều này có thể dẫn đến lỗi gọi là **dirty read** (đọc bẩn).

📌 **Ví dụ:**

* Giao dịch A chèn một bản ghi xe mới nhưng chưa commit
* Giao dịch B đọc số lượng xe hiện có và thấy bản ghi của A → thêm xe vào kho
* Sau đó, Giao dịch A rollback → xe chưa từng tồn tại, nhưng đã được thêm vào kho!

🔴 Kết quả: **Tạo ra dữ liệu không thực sự tồn tại**.

---

## 2. **Read Committed** (Đọc dữ liệu đã cam kết)

Giao dịch chỉ có thể đọc dữ liệu đã được commit trước khi giao dịch bắt đầu. **Tránh được lỗi dirty read**.

Tuy nhiên, nếu một giao dịch khác commit dữ liệu trong khi bạn đang thực hiện truy vấn, bạn **vẫn có thể thấy dữ liệu thay đổi giữa hai lần đọc**, dù nằm trong cùng một giao dịch. Đây là lỗi **non-repeatable read** (đọc không lặp lại được).

📌 **Ví dụ:**

* Giao dịch A đọc bản ghi "Toyota" → vẫn thấy bản ghi
* Trong lúc đó, Giao dịch B đổi tên Toyota thành Jeep và commit
* Giao dịch A đọc lại → dữ liệu đã thay đổi → không còn như ban đầu

---

## 3. **Repeatable Read** (Đọc lặp lại được)

Đảm bảo rằng **dữ liệu đã đọc sẽ không thay đổi** trong suốt thời gian giao dịch. Tránh được lỗi **dirty read** và **non-repeatable read**.

Tuy nhiên, giao dịch vẫn có thể **nhìn thấy bản ghi mới** nếu truy vấn lại. Đây là lỗi **phantom read** (đọc bóng ma).

📌 **Ví dụ:**

* Giao dịch A đọc danh sách xe "Toyota" → thấy 1 dòng
* Giao dịch B thêm một xe Toyota mới và commit
* Giao dịch A đọc lại → thấy thêm bản ghi mới

🔴 Giao dịch A không thấy bản ghi bị sửa, nhưng thấy bản ghi mới được thêm → lỗi phantom.

---

## 4. **Serializable** (Tuần tự hóa)

Đây là mức độ cô lập **cao nhất**. Nó đảm bảo **giao dịch được thực hiện tuần tự**, như thể tất cả xảy ra lần lượt thay vì đồng thời.

✅ Không có lỗi dirty read, non-repeatable read, hay phantom.

📌 Nhược điểm: tốn tài nguyên, giảm hiệu năng hệ thống vì phải khóa dữ liệu chặt chẽ hơn.

---

## **Tổng kết so sánh các mức độ cô lập:**

| Mức độ cô lập    | Dirty Read (Đọc bẩn) | Non-repeatable Read (Đọc không lặp lại) | Phantom (Bóng ma) |
| ---------------- | -------------------- | --------------------------------------- | ----------------- |
| Read Uncommitted | Có thể xảy ra        | Có thể xảy ra                           | Có thể xảy ra     |
| Read Committed   | ❌ Không xảy ra       | Có thể xảy ra                           | Có thể xảy ra     |
| Repeatable Read  | ❌ Không xảy ra       | ❌ Không xảy ra                          | Có thể xảy ra     |
| Serializable     | ❌ Không xảy ra       | ❌ Không xảy ra                          | ❌ Không xảy ra    |

---

📌 **Lưu ý:** Mức độ cô lập cao sẽ ảnh hưởng đến hiệu suất. Cô lập càng cao:

* ❗ Giao dịch càng lâu kết thúc (tăng **độ trễ** – *latency*)
* ❗ Càng ít giao dịch được xử lý cùng lúc (giảm **tốc độ xử lý** – *throughput*)

---

🎉 **Bạn đã nắm rõ về các mức độ cô lập giao dịch!** Sẵn sàng luyện tập?

---

## **Mức độ cô lập giao dịch trong PostgreSQL**

PostgreSQL hỗ trợ đầy đủ các mức độ cô lập giao dịch tiêu chuẩn theo chuẩn SQL, bao gồm:

* **Read Uncommitted**
* **Read Committed** (mặc định)
* **Repeatable Read**
* **Serializable**

---

### 1. **Read Uncommitted** trong PostgreSQL

* Mặc dù PostgreSQL hỗ trợ mức này về mặt cú pháp, nhưng thực chất nó được xử lý tương tự **Read Committed**.
* PostgreSQL không cho phép đọc dữ liệu chưa được commit (không xảy ra **dirty read**).
* Nếu bạn đặt mức này, nó tự động hạ xuống Read Committed.

---

### 2. **Read Committed** (Mặc định)

* Mỗi câu truy vấn trong giao dịch sẽ nhìn thấy dữ liệu đã được commit **tại thời điểm câu truy vấn đó bắt đầu**.
* Nếu thực hiện nhiều truy vấn trong một giao dịch, mỗi truy vấn có thể thấy dữ liệu khác nhau nếu có các giao dịch khác commit dữ liệu mới giữa thời điểm đó.
* Tránh được **dirty read** nhưng có thể xảy ra **non-repeatable read** và **phantom read**.

**Cách thiết lập:**

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

---

### 3. **Repeatable Read**

* Toàn bộ giao dịch nhìn thấy một snapshot dữ liệu cố định tại thời điểm bắt đầu giao dịch.
* Tránh được **dirty read** và **non-repeatable read**.
* Tuy nhiên, vẫn có thể xảy ra **phantom read** — nghĩa là nếu bạn chạy truy vấn lọc dữ liệu, những bản ghi mới thêm vào sau đó vẫn có thể xuất hiện nếu truy vấn được chạy lại.

**Cách thiết lập:**

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

---

### 4. **Serializable**

* Mức độ cô lập cao nhất, đảm bảo các giao dịch thực thi tuần tự như thể chạy một mình.
* Tránh tất cả các lỗi: **dirty read**, **non-repeatable read**, **phantom read**.
* PostgreSQL sử dụng kỹ thuật gọi là **Serializable Snapshot Isolation (SSI)** để phát hiện và ngăn chặn các giao dịch gây xung đột.
* Nếu xảy ra xung đột, giao dịch sẽ bị **rollback** và bạn cần retry lại.

**Cách thiết lập:**

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

---

## **Cách sử dụng trong thực tế**

Bạn có thể đặt mức độ cô lập cho từng giao dịch cụ thể bằng lệnh:

```sql
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- các câu lệnh SQL khác

COMMIT;
```

Hoặc thay đổi mặc định cho phiên làm việc hiện tại:

```sql
SET SESSION CHARACTERISTICS AS TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

---

## **Lưu ý**

* **Read Committed** là mức mặc định, phù hợp cho hầu hết ứng dụng vì cân bằng tốt giữa hiệu năng và độ nhất quán.
* Khi dùng **Serializable**, bạn cần xử lý tình huống giao dịch bị rollback do xung đột (thường bằng cách retry).
* Các mức cao hơn sẽ giữ snapshot dữ liệu ổn định, giảm lỗi đồng thời nhưng cũng làm tăng khả năng chờ khóa và giảm throughput.

