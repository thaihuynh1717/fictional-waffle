
---

**Tóm tắt: Chỉ mục trong Cơ sở Dữ liệu**

Khi cơ sở dữ liệu phát triển, thời gian thực hiện các truy vấn sẽ tăng lên. Để giải quyết vấn đề hiệu suất này, hầu hết các hệ quản trị cơ sở dữ liệu hiện đại đều sử dụng **chỉ mục (index)** – một cấu trúc dữ liệu đặc biệt giúp tăng tốc truy vấn đọc.

### 1. Ví dụ thực tế

Ví dụ như trong thư viện, mỗi cuốn sách đều có thẻ thông tin giúp thủ thư dễ dàng tra cứu thay vì phải tìm từng cuốn. Chỉ mục trong cơ sở dữ liệu hoạt động tương tự như các thẻ này.

### 2. Chỉ mục trong cơ sở dữ liệu

* Chỉ mục là một cấu trúc dữ liệu được tạo từ một hoặc nhiều thuộc tính và trỏ tới các bản ghi tương ứng.
* Chúng giúp truy vấn nhanh hơn nhưng cần thêm bộ nhớ và làm chậm quá trình thêm/cập nhật dữ liệu.
* Cả cơ sở dữ liệu quan hệ (Relational) và NoSQL đều hỗ trợ chỉ mục.

### 3. Phân loại chỉ mục

#### Theo thuộc tính:

* **Đơn giản (Simple)**: Dựa trên một thuộc tính.
* **Phức hợp (Compound/Composite)**: Dựa trên nhiều thuộc tính.
* **Clustered**: Thay đổi cách sắp xếp vật lý của dữ liệu, chỉ có một chỉ mục duy nhất kiểu này trên mỗi bảng.
* **Non-clustered**: Không thay đổi dữ liệu vật lý, chỉ chứa con trỏ tới bản ghi.
* **Unique**: Đảm bảo không có giá trị trùng lặp.
* **Partial**: Áp dụng với một phần dữ liệu thỏa điều kiện nhất định.

#### Theo cấu trúc dữ liệu:

* **B-Tree**: Loại phổ biến nhất, hỗ trợ so sánh và sắp xếp. Dùng được với số, chuỗi, ngày tháng,… nhưng bị giới hạn với tìm kiếm chuỗi không bắt đầu từ ký tự đầu.
* **Hash**: Nhanh hơn B-Tree khi tìm kiếm bằng khóa chính xác (equality), nhưng không hỗ trợ so sánh (<, >) hay sắp xếp.
* **Bitmap**: Tốt khi giá trị thuộc tính có ít giá trị khác biệt (low cardinality), như giới tính, trạng thái.
* **Inverted**: Dùng trong tìm kiếm văn bản (full-text search) hoặc mảng, ánh xạ từ từ khóa tới danh sách các bản ghi chứa từ đó.
* **Spatial**: Dùng cho dữ liệu không gian như vị trí địa lý (ví dụ: tìm các bảo tàng trong bán kính 5km).

### 4. Kết luận

Chỉ mục giúp tăng tốc độ đọc dữ liệu nhưng tốn thêm dung lượng lưu trữ và làm chậm thao tác ghi (insert/update). Mỗi loại chỉ mục phù hợp với những tình huống khác nhau và có thể hoạt động hơi khác tùy thuộc vào hệ quản trị cơ sở dữ liệu cụ thể.

---

Dưới đây là minh hoạ các loại chỉ mục trong PostgreSQL bằng sơ đồ và ví dụ thực tế:

---

## 1. B‑Tree Index (mặc định)

* **Sơ đồ** (hình 1): cây cân bằng, mỗi nút chứa key và con trỏ tới child hoặc tới dòng dữ liệu ([PostgreSQL][1], [Redgate Software][2]).
* **Ví dụ tạo chỉ mục**:

  ```sql
  CREATE INDEX idx_book_published_on
    ON book (published_on) INCLUDE (title);
  ```
* **Sử dụng**: hiệu quả cho truy vấn `=`, `<`, `>`, `BETWEEN`, `ORDER BY` ([Postgres Professional][3]).

---

## 2. Hash Index

* **Đặc điểm**:

  * Sử dụng hàm băm để ánh xạ giá trị tới bucket.
  * Rất nhanh cho truy vấn equality (`=`), O(1); không hỗ trợ so sánh khoảng .
* **Ví dụ**:

  ```sql
  CREATE INDEX idx_book_title_hash
    ON book USING hash (title);
  ```
* **Hạn chế**: không hỗ trợ `>`, `<`, kém phổ biến.

---

## 3. GIN (Generalized Inverted Index) – cho Full‑text, JSONB, array

* **Sơ đồ** (hình 4): ánh xạ từ từng từ/term tới danh sách ID bản ghi chứa nó ([Postgres Professional][3], [imdeepmind.com][4]).
* **Ví dụ full-text**:

  ```sql
  ALTER TABLE posts ADD COLUMN body_search tsvector;
  UPDATE posts SET body_search = to_tsvector('english', body);
  CREATE INDEX idx_body_fts ON posts USING GIN(body_search);
  SELECT * FROM posts
    WHERE body_search @@ to_tsquery('english','basic | advanced');
  ```


* **Ví dụ JSONB**:

  ```sql
  CREATE TABLE routes_jsonb(route jsonb);
  CREATE INDEX idx_route_jsonb ON routes_jsonb USING GIN(route);
  SELECT * FROM routes_jsonb
    WHERE route @> '{"days_of_week":[5]}';
  ```

  ([Postgres Professional][3])
* **Ứng dụng**: tìm kiếm từ trong văn bản, các phần tử trong JSON/array.

---

## 4. Bitmap (thường nội bộ của PostgreSQL)

* **Cách hoạt động**: tạo bitmap mask đánh dấu bản ghi thỏa điều kiện, sau đó kết hợp bitmaps với toán bitwise .
* **Phù hợp** với các cột *low-cardinality* (ví dụ: boolean, giới tính).

---

## 5. Spatial, GiST, SP-GiST, BRIN etc.

* PostgreSQL hỗ trợ các kiểu khác như GiST (full-text, not-inverted), SP-GiST (k‑d tree), BRIN (block-range) cho các trường hợp đặc biệt như dữ liệu địa lý không gian.
  Ví dụ R‑Tree dùng để tìm “tìm bảo tàng trong bán kính 5 km” v.v.

---

## 📚 Tóm tắt

| Loại chỉ mục          | Sử dụng                                            | Ví dụ                              |
| --------------------- | -------------------------------------------------- | ---------------------------------- |
| **B‑Tree**            | Equality, range, sort                              | `CREATE INDEX … USING btree (...)` |
| **Hash**              | Chỉ equality                                       | `CREATE INDEX … USING hash (...)`  |
| **GIN**               | Full-text, JSONB, array                            | `CREATE INDEX … USING gin(...)`    |
| **Bitmap**            | Nội bộ PostgreSQL kết hợp nhiều điều kiện          |                                    |
| **GiST/SP‑GiST/BRIN** | Dữ liệu không gian, văn bản, dữ liệu lớn phân vùng |                                    |

---

Sau khi thêm chỉ mục, PostgreSQL có thể chọn kế hoạch truy vấn tối ưu. Tuy nhiên, đừng quên rằng mỗi chỉ mục sẽ:

* Tốn thêm dung lượng đĩa,
* Làm chậm ghi (INSERT/UPDATE/DELETE).
