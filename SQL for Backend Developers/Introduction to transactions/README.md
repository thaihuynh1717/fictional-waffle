## **Giới thiệu**

Bạn có thể đã từng nghe đến **giao dịch (transaction)** trong cuộc sống. Có thể từ này khiến bạn liên tưởng đến việc mua bán, hoặc giao dịch ngân hàng với việc tiền được chuyển qua lại giữa các tài khoản. Về cơ bản, **giao dịch** là một chuỗi các thao tác hợp lý chỉ có thể được thực hiện trọn vẹn hoặc không thực hiện gì cả.

Trong số các loại giao dịch, có một loại đặc biệt được gọi là **giao dịch cơ sở dữ liệu**.

**Giao dịch cơ sở dữ liệu** là một đơn vị công việc trong hệ quản trị cơ sở dữ liệu (DBMS), thực hiện các thao tác thay đổi dữ liệu như thêm, cập nhật, hoặc xóa. Trong bài học này, chúng ta sẽ tìm hiểu giao dịch là gì và vì sao nó rất quan trọng.

---

## **Khái niệm giao dịch**

Hãy tưởng tượng một hệ thống thanh toán đơn giản. Bạn đang mua cà phê bằng ví điện tử. Quá trình này có thể được chia thành hai phần:

1. Số tiền mua cà phê được trừ khỏi tài khoản ngân hàng của bạn.
2. Số tiền đó được chuyển tới tài khoản của người bán.

Nếu một trong hai bước thất bại (ví dụ như lỗi mạng hoặc lỗi kỹ thuật), thì giao dịch **phải bị hủy bỏ toàn bộ**. Tức là, **hoặc cả hai thao tác xảy ra, hoặc không gì xảy ra cả**.

Quá trình này được mô tả như sau:

```
Start
   ↓
Operation 1 → Operation 2 → ... → Operation N
   ↓            ↓                 ↓
 Commit     Commit          Commit

Hoặc nếu lỗi:
   ↓
 Rollback → Trở về trạng thái trước
```

Vì vậy, một giao dịch là một đơn vị công việc. Nếu thành công, tất cả thay đổi dữ liệu sẽ được lưu lại. Nếu thất bại, mọi thay đổi bị hủy bỏ.

**Nguyên tắc chính của giao dịch là: Tất cả hoặc không gì cả.**

---

## **Thuộc tính của giao dịch**

Một giao dịch được đặc trưng bởi 4 thuộc tính, thường gọi là **ACID**:

| Thuộc tính                       | Mô tả                                                                                                           |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Atomicity (Tính nguyên tử)**   | Tất cả thao tác trong giao dịch phải được thực hiện hoàn toàn. Nếu một phần thất bại, toàn bộ giao dịch bị hủy. |
| **Consistency (Tính nhất quán)** | Giao dịch đưa dữ liệu từ trạng thái hợp lệ này sang trạng thái hợp lệ khác.                                     |
| **Isolation (Tính cô lập)**      | Giao dịch không bị ảnh hưởng bởi các giao dịch khác đang diễn ra đồng thời.                                     |
| **Durability (Tính bền vững)**   | Khi giao dịch thành công, mọi thay đổi sẽ được lưu vĩnh viễn vào hệ thống.                                      |

---

## **Trạng thái của giao dịch**

Trong vòng đời của giao dịch, nó sẽ trải qua nhiều trạng thái:

* **Active (Đang hoạt động)** – Giao dịch đang thực hiện các thao tác.
* **Partially Committed (Cam kết một phần)** – Một số thao tác đã hoàn thành, sắp sửa hoàn tất.
* **Committed (Cam kết)** – Giao dịch đã hoàn tất, mọi thay đổi được lưu vĩnh viễn.
* **Failed (Thất bại)** – Một thao tác trong giao dịch bị lỗi.
* **Aborted (Bị hủy)** – Giao dịch bị hủy và quay về trạng thái ban đầu.

### Bảng mô tả trạng thái:

| Trạng thái              | Mô tả                                                     |
| ----------------------- | --------------------------------------------------------- |
| **Active**              | Giao dịch đang hoạt động, thực hiện các thao tác.         |
| **Partially Committed** | Một phần thao tác đã hoàn tất, chuẩn bị kết thúc.         |
| **Failed**              | Một thao tác bị lỗi, cần phục hồi.                        |
| **Aborted**             | Giao dịch đã bị hủy, không còn thay đổi nào.              |
| **Committed**           | Giao dịch thành công và dữ liệu đã được ghi vào hệ thống. |

---

**Lưu ý:** Việc ghi nhớ chính xác trạng thái giao dịch rất quan trọng vì ảnh hưởng đến hiệu suất và độ tin cậy hệ thống.

Ví dụ: Khi có lỗi, hệ thống sẽ phục hồi về trạng thái trước đó nhờ khả năng "rollback". Nếu thành công, mọi dữ liệu được ghi lại bền vững ("commit").

---

## **Kết luận**

Giao dịch nhóm nhiều thao tác lại và thực hiện chúng như một đơn vị. Nếu một thao tác thất bại, toàn bộ giao dịch thất bại. Nếu tất cả thành công, giao dịch sẽ được cam kết. Điều này đảm bảo **toàn vẹn và tin cậy** cho cơ sở dữ liệu.

---

## 🔍 **Giải thích chi tiết thuộc tính ACID**

ACID là viết tắt của:

| Tên thuộc tính                        | Giải thích dễ hiểu                                                                                                                   | Ví dụ thực tế                                                                                                                                                           |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🅰️ **Atomicity** *(Tính nguyên tử)*  | Giao dịch là **toàn bộ hoặc không gì cả**. Nếu một phần thất bại, **toàn bộ bị hủy bỏ**.                                             | Bạn chuyển tiền qua ứng dụng ngân hàng. Nếu tiền bị trừ khỏi tài khoản bạn **nhưng không vào tài khoản người nhận**, hệ thống sẽ hoàn tác toàn bộ giao dịch.            |
| 🅲 **Consistency** *(Tính nhất quán)* | Dữ liệu trước và sau giao dịch phải **luôn đúng** với quy tắc hệ thống. Giao dịch không được khiến dữ liệu bị “hỏng” hoặc sai logic. | Sau khi mua hàng, hệ thống phải đảm bảo bạn bị trừ đúng số tiền và người bán nhận đúng số lượng tiền. Không được có tình trạng mất tiền mà không có giao dịch ghi nhận. |
| 🅸 **Isolation** *(Tính độc lập)*     | Giao dịch này **không bị ảnh hưởng** bởi giao dịch khác đang chạy song song.                                                         | Nếu bạn và người khác cùng mua một sản phẩm còn đúng 1 cái, chỉ một người nên thành công. Hệ thống phải xử lý sao cho không bị trùng lặp hay mâu thuẫn.                 |
| 🅳 **Durability** *(Tính bền vững)*   | Khi giao dịch thành công, **mọi thay đổi sẽ được lưu vĩnh viễn**, ngay cả khi mất điện hay hệ thống gặp sự cố.                       | Sau khi bạn chuyển tiền và nhận được thông báo "thành công", dữ liệu đó sẽ còn tồn tại trong hệ thống, không bao giờ mất đi.                                            |

---

## 🔄 **Giải thích kỹ các trạng thái giao dịch**

Dưới đây là các trạng thái quan trọng mà một giao dịch có thể trải qua, cùng ví dụ minh họa:

| Trạng thái                                   | Mô tả chi tiết                                                                                           | Ví dụ                                                                                                  |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Active** *(Đang hoạt động)*                | Giao dịch bắt đầu và đang thực hiện các thao tác như thêm, sửa, xóa.                                     | Hệ thống đang xử lý thao tác “trừ tiền” trong ví của bạn.                                              |
| **Partially Committed** *(Cam kết một phần)* | Một số thao tác đã thành công, nhưng chưa ghi chính thức vào cơ sở dữ liệu.                              | Đã trừ tiền nhưng chưa ghi vào lịch sử giao dịch.                                                      |
| **Committed** *(Đã cam kết)*                 | Giao dịch **hoàn tất**. Tất cả thay đổi đã ghi **vĩnh viễn** vào hệ thống.                               | Giao dịch chuyển tiền đã hoàn tất. Số tiền đã trừ và ghi vào lịch sử, người nhận cũng đã nhận được.    |
| **Failed** *(Thất bại)*                      | Một lỗi xảy ra trong quá trình. Có thể do mất kết nối, lỗi logic, hết tiền...                            | Không thể trừ tiền vì số dư không đủ.                                                                  |
| **Aborted** *(Bị hủy)*                       | Giao dịch bị hủy hoàn toàn, hệ thống phục hồi về trạng thái ban đầu. Không có thay đổi nào được giữ lại. | Giao dịch chuyển tiền bị lỗi, và số dư tài khoản quay lại như cũ. Không có ghi nhận nào trong lịch sử. |

🔁 Một điểm quan trọng là: **giao dịch chỉ được chuyển sang trạng thái "committed" khi tất cả các thao tác đều thành công. Nếu không, nó sẽ chuyển sang "aborted".**

---

## 📌 Tại sao điều này quan trọng?

* **Bảo vệ dữ liệu người dùng**: Không ai muốn tiền biến mất vì một lỗi kỹ thuật.
* **Tránh xung đột dữ liệu khi nhiều người cùng thao tác**.
* **Tăng độ tin cậy hệ thống**: Dù mất điện, dữ liệu vẫn an toàn.

---
