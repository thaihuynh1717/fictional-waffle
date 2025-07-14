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

