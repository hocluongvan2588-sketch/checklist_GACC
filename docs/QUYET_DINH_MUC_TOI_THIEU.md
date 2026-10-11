# Quyết định phạm vi tối thiểu — "AI chỉ giúp soi thiếu, không kết luận"

*Tài liệu này trả lời câu hỏi: "AI quét nội dung đối chiếu checklist để biết còn thiếu mục nào — nhưng vẫn có
sai sót, vậy có làm được không?"*

---

## 1. Câu hỏi thật sự không phải "AI có sai không"

AI **chắc chắn sẽ sai**. Câu hỏi quyết định là:

> **Khi AI sai, ai phát hiện, phát hiện trong bao lâu, và thiệt hại là bao nhiêu?**

Nếu thiết kế đúng, sai của AI chủ yếu rơi vào loại **"báo thiếu nhầm"** — chuyên viên nhìn một cái biết ngay,
tốn thêm 5–10 giây. Nếu thiết kế sai (để AI tự kết luận "đạt/đủ"), sai rơi vào loại **"báo đủ nhầm"** —
không ai phát hiện cho đến khi cơ quan từ chối hồ sơ. **Cái thứ hai mới là thứ không thể thực thi.**

→ **Nguyên tắc thiết kế: lệch về phía an toàn (asymmetric error design).**

| Loại lỗi | Nghĩa là | Chi phí | Chấp nhận? |
| --- | --- | --- | --- |
| **Báo thiếu nhầm** (false negative) | Đã có tài liệu nhưng AI nói "chưa thấy" | Chuyên viên mở đúng file, tick lại. ~5–10 giây/mục | ✅ Chấp nhận, thậm chí chủ động làm hệ thống "nhát gấp" |
| **Báo sai loại tài liệu** | Nhầm giấy phép thành chứng chỉ | 1 click sửa | ✅ Chấp nhận nếu ≤ 15% |
| **Gợi ý không liên quan** | Dẫn đến trang không đúng | Bỏ qua, vài giây | ✅ Chấp nhận nếu ≤ 40% |
| **Báo đủ nhầm / tự kết luận đạt** | Không có tài liệu nhưng AI nói "đã có minh chứng" | **Không phát hiện được** → rủi ro pháp lý & uy tín | 🔴 **Không bao giờ cho phép** |

---

## 2. Chia checklist thành 2 tầng — đây là mấu chốt để "thực thi được"

Cùng một câu "đối chiếu với checklist", nhưng có **2 việc rất khác nhau về độ khó**:

| | **Tầng 1 — Đối chiếu mức tài liệu** | **Tầng 2 — Đánh giá mức nội dung** |
| --- | --- | --- |
| Câu hỏi | *"Có tài liệu X không? Còn hạn không? Số đúng chưa?"* | *"Nội dung tài liệu X có thực sự đáp ứng yêu cầu Y không?"* |
| Ví dụ gạo | Đã có Giấy CN cơ sở đủ ĐK ATTP? HACCP còn hạn tới ngày nào? Phiếu KQ kiểm nghiệm cadimi có không? | HACCP plan đã mô tả kiểm soát kim loại nặng đầy đủ chưa? |
| Bản chất kỹ thuật | Phân loại **tập đóng** (15–20 loại tài liệu cố định) + trích 3–5 trường | Đọc–hiểu–diễn giải → **cần chuyên gia** |
| Độ đúng kỳ vọng (scan khá) | **85–95%** | **60–80%** (chỉ ở mức "dẫn đến đúng chỗ", không phải kết luận) |
| Lỗi | Nhìn thấy ngay, sửa 1 click | Cần chuyên viên đọc |
| Tự động hóa | ✅ Nên làm | ⚠️ Chỉ làm phần **"dẫn chuyên viên đến đúng trang"**, không kết luận |

**Kết luận:** việc bạn cần ("quét xem còn thiếu mục nào") rơi **chủ yếu vào Tầng 1** — và Tầng 1 **làm được**,
với độ tin cậy đủ để tiết kiệm thời gian thật. Tầng 2 là phần đặc tả đang tham vọng quá; ở MVP chỉ nên làm
nửa chừng: gợi ý trang/đoạn, người quyết.

---

## 3. Kỳ vọng thực tế theo từng việc (ước lượng để không bị vỡ mộng)

| Việc | Độ đúng kỳ vọng | Ghi chú |
| --- | --- | --- |
| Tách file, phát hiện trùng, đặt tên chuẩn | ~95% | Cơ học, ít rủi ro |
| Phân loại loại tài liệu (tập đóng 15–20 loại) | 85–93% | Đo trên chính hồ sơ của Vexim mới biết |
| Trích 3–5 trường chính (số, ngày cấp, ngày hết hạn, tên cơ sở) | 80–92% | Thấp hơn đáng kể nếu scan mờ |
| Kiểm tra nhất quán chéo (tên/địa chỉ/người đại diện khác nhau giữa các tài liệu) | ~90% | Rất giá trị, ít rủi ro pháp lý |
| Gợi ý "tài liệu này **có thể** liên quan mục X" | 60–80% | Chỉ để nhảy đến đúng chỗ |
| Tự kết luận "đạt/không đạt" | **Không làm** | Bất biến của hệ thống |

**Diễn giải thô:** với một bộ hồ sơ 40 tài liệu, hệ thống Tầng 1 làm đúng ~34–38 file, sai 2–6 file. Chuyên viên
vẫn phải nhìn qua tất cả để xác nhận — nhưng **không phải mở từng file để tìm xem nó là gì và nằm ở đâu**.
Giá trị nằm ở chỗ đó, không nằm ở "AI làm thay".

---

## 4. Ba phương án — và phương án tôi khuyên

| | **P0 — Không xây** | **P1 — Công cụ nhẹ (khuyên dùng)** | **P2 — Nền tảng như đặc tả** |
| --- | --- | --- | --- |
| Làm gì | OCR hàng loạt + Excel thủ công | Upload → OCR → phân loại → **bảng "có/chưa thấy/cần xem"** theo checklist → export Excel | Thêm evidence cấp nội dung + trích dẫn + sửa tay + yêu cầu bổ sung + pack admin + audit |
| Nỗ lực | 0 | **4–6 person-month** (~6–10 tuần, 2 người) | 32–48 person-month (6–9 tháng, 5–6 người) |
| Chi phí phần mềm | 0 | **~12–25 nghìn USD** | ~80–220 nghìn USD |
| Thời gian chuyên gia Vexim | – | ~8–12 ngày công (chủ yếu làm chuẩn checklist) | ~25–40 ngày công |
| Rủi ro thất bại | – | **Thấp** (phạm vi hẹp, đo được ngay) | Trung bình–cao |
| Điều kiện phải có | – | 1 người chịu trách nhiệm checklist + 20–30 bộ hồ sơ để đo | Chủ sở hữu nội dung + quy trình duyệt pack + câu trả lời 12 câu hỏi |

**Khuyến nghị: làm P1, và bắt đầu bằng 2 tuần đo thử trước khi viết code sản phẩm.**

---

## 5. P1 trông như thế nào (cụ thể)

### Có

1. Tạo "gói kiểm tra" (checklist) từ file Excel có sẵn của Vexim: mỗi dòng = một mục cần có (tên tài liệu,
   loại, có bắt buộc không, ghi chú).
2. Tải lên toàn bộ file của một khách hàng; hệ thống OCR/đọc text, **phân loại file vào tập đóng** các loại
   tài liệu (dựa trên cấu hình của gói kiểm tra, không hard-code "gạo").
3. Trích vài trường đơn giản: số giấy, ngày cấp, **ngày hết hạn**, tên cơ sở → tự động cờ "sắp hết hạn/hết hạn".
4. **Bảng đối chiếu**: mỗi dòng checklist × 3 trạng thái:
   - `Có — đã nhận diện` (kèm tên file, trang, và đoạn text làm bằng chứng nhận diện)
   - `Chưa thấy` ← mặc định khi không chắc
   - `Cần xem` ← nghi ngờ, không chắc chắn
   - **Không có trạng thái "Đạt".**
5. Một cột "Gợi ý liên quan": những file có khả năng liên quan để chuyên viên bấm vào xem ngay.
6. Cảnh báo mâu thuẫn: cùng một thông tin (tên/địa chỉ/người đại diện/số giấy) mà khác nhau giữa các tài liệu.
7. Export Excel đúng mẫu Vexim + PDF có ghi rõ: *kết quả rà soát nội bộ, do hệ thống hỗ trợ, không phải kết
   luận của cơ quan*.

### Không có (cắt hẳn ở P1)

- ❌ AI tự đánh giá nội dung có đáp ứng yêu cầu hay không
- ❌ Tự động gửi gì cho khách / cổng khách hàng
- ❌ Workflow duyệt phức tạp, phân quyền nhiều cấp
- ❌ Tích hợp CRM/Drive/chữ ký số
- ❌ Đa ngôn ngữ nâng cao (bản dịch chỉ để đọc, luôn hiển thị kèm bản gốc)

### Cách chứng minh trước khi trả tiền (2 tuần)

1. Lấy **20–30 bộ hồ sơ đã ẩn danh** (hoặc xin phép 2–3 khách).
2. Chạy thử 2–3 engine OCR/đọc text trên chính số hồ sơ đó.
3. Đo 4 con số: (a) phân loại đúng bao nhiêu %, (b) trích đúng ngày/số bao nhiêu %, (c) "chưa thấy" có bỏ sót
   tài liệu thật không, (d) thời gian xử lý & chi phí/bộ.
4. **Ngưỡng để tiếp tục:** phân loại ≥ 85%, không bỏ sót (recall) ≥ 90%, chi phí ≤ 5 USD/bộ.
   Không đạt ⇒ dừng ở P0, không mất gì ngoài 2 tuần.

---

## 6. Trả lời thẳng câu hỏi của bạn

| Kỳ vọng | Khả thi? |
| --- | --- |
| "AI đọc hồ sơ rồi kết luận đạt/thiếu, tôi tin và gửi đi" | **Không.** Đừng làm — đây là phần tôi khuyên bỏ khỏi đặc tả |
| "AI làm lượt đầu: đặt tên, phân loại, map vào checklist, đánh dấu những mục chưa thấy, và đưa tôi đến đúng trang; tôi xác nhận từng dòng" | **Có.** Đây là P1, làm được trong ~6–10 tuần |
| "AI soi được cả nội dung có đáp ứng yêu cầu không" | **Một phần** — chỉ nên làm đến mức gợi ý trang/đoạn; phần kết luận vẫn là người |

**Và một sự thật khác cần nói thẳng:** nếu Vexim không có một người chịu trách nhiệm duy trì cái checklist đó
(người sửa khi GACC đổi quy định — mà họ vừa đổi xong: Lệnh 248 → Lệnh 280), thì dự án sẽ chết **vì nội dung,
không phải vì AI**. Vấn đề này lớn hơn vấn đề sai số của AI, và nó nằm hoàn toàn trong tay Vexim.

---

## 7. Khi nào nên hủy hẳn dự án

- Không có người chịu trách nhiệm nội dung checklist → hủy (hoặc hoãn đến khi có).
- Không có bộ hồ sơ thật để đo (không ai đồng ý cho dùng, kể cả ẩn danh) → hủy; không có cách nào biết AI
  tốt đến đâu.
- Chuyên viên từ chối quy trình "xác nhận từng dòng" và muốn hệ thống tự chạy xong là xong → hủy.
- Sau 2 tuần đo thử mà phân loại < 70% hoặc hay bỏ sót tài liệu → **tạm dừng**, chỉ giữ lại phần OCR + đặt tên
  file (đã tiết kiệm được thời gian), chờ công nghệ/dữ liệu tốt hơn.
