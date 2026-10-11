# Kế hoạch triển khai — Công cụ rà soát hồ sơ đăng ký cơ sở xuất khẩu thực phẩm sang Trung Quốc

**Ngày lập:** 11/10/2026 · **Cơ sở:** danh sách 40 mục theo tài liệu Cục BVTV + danh sách 148 mục theo biểu mẫu gốc Trung Quốc
**Phạm vi:** từ hôm nay đến khi có công cụ dùng được cho ca làm việc thật (~4–5 tháng), sau đó mở rộng

---

## 0. Năm quy tắc bất di bất dịch (lấy thẳng từ tài liệu của Vexim)

Đây không phải sáng tác của nhóm kỹ thuật — đây là 5 câu có sẵn trong tài liệu bạn gửi. Cần cài vào hệ thống dưới dạng **luật không được vi phạm**, có test tự động.

| # | Quy tắc | Cách cài |
| --- | --- | --- |
| **R1** | *"Một tài liệu có thể làm bằng chứng cho nhiều mục… từng yêu cầu vẫn được rà riêng"* | Cấu trúc many-to-many: 1 tệp ↔ N mã mục; **mỗi mã mục có trạng thái riêng**, không gộp |
| **R2** | *"Không tự tạo hồ sơ âm tính"* (mục 12.2) | **Không bao giờ sinh bản ghi/giá trị.** Thiếu thì ghi `Chưa thấy trong hồ sơ hiện có` |
| **R3** | *"Không cần tự soạn thay khi chưa được hướng dẫn"* | **Cấm** nút "tự động soạn tài liệu thay doanh nghiệp" ở giai đoạn 1–2 |
| **R4** | *"Các mục có 'nếu có', 'nếu áp dụng'… Nếu không có, ghi Không áp dụng và nêu lý do"* | 9 mục điều kiện: máy chỉ hỏi khi thuộc tính case tương ứng = có; quyết định `Không áp dụng` **là của người** |
| **R5** | *"Việc hồ sơ có được tiếp nhận hay không do cơ quan có thẩm quyền xem xét"* | Không có trạng thái "Đạt". Mọi output kèm disclaimer + phiên bản checklist + ngày lập |

---

## 1. Cốt lõi của MVP: một việc, làm cho xong

> **Bỏ vào 50–120 file của một doanh nghiệp → máy tự điền BẢNG KÊ TÀI LIỆU và chỉ ra mã mục nào chưa có tài liệu nào.**

Đó là toàn bộ MVP. Không hơn. Tài liệu của bạn đã định nghĩa sẵn output:

| STT | Tên tệp/tài liệu | Mã mục liên quan | Ghi chú / ngày tài liệu | Độ tin cậy | Xác nhận |
|---|---|---|---|---|---|
| 1 | `GPKD_CongtyA_2023.pdf` | **A1**, 1, 13 | cấp 12/03/2023 | 0,94 | ☑ Chuyên viên |
| 2 | `CN_ATTP_TT38_2024.pdf` | **A4** | hết hạn 30/06/2027 | 0,91 | ☑ |
| 3 | `So_do_quy_trinh_v2.pdf` | **A3**, **6.1.1** | bản 2 · 05/2026 | 0,88 | ☐ |
| 4 | `Nhatky_ditru_2025-2026.xlsx` | **8.3**, 12.3 | đủ 14 tháng | 0,76 | ☐ |
| … | | | | | |
| — | *(không tìm thấy)* | **9.1** | ⚠ Chưa có tài liệu nào | — | ☐ |

Và mặt còn lại của nó — **ma trận 40 mục**, tự động điền sẵn:

| Mã mục | Diễn giải | Tài liệu ứng viên | Trạng thái đề xuất | Ghi chú |
|---|---|---|---|---|
| A4 | Chứng nhận ATTP | `CN_ATTP_TT38_2024.pdf` | Đã có (còn hạn đến 30/06/2027) | Thuộc danh sách được chấp nhận |
| 3.2 | Kho lạnh | — | Không áp dụng? | Case chưa khai có kho lạnh → hỏi lại |
| 9.1 | Truy xuất + ví dụ mã lô | `Quytrinh_truyxuat.pdf` | Cần bổ sung | **Có quy trình nhưng thiếu ví dụ mã lô thật** |
| 12.2 | Hồ sơ dịch hại phát hiện | — | Chưa thấy trong hồ sơ hiện có | ⚠ Không tự tạo bản ghi âm tính |

Hai bảng này sinh ra từ cùng một lần xử lý. Làm xong hai bảng này là bạn đã tiết kiệm được phần việc tốn thời gian nhất.

---

## 2. Lộ trình 4 giai đoạn

```
G0  Chốt nền tảng        2–3 tuần     (chủ yếu Vexim)
 │
 ├── G1  PoC đo chất lượng   4–6 tuần     (1–2 dev) ──▶ Go / No-Go
 │
 ├── G2  Công cụ mức 1      10–14 tuần    (2 người) ──▶ DÙNG ĐƯỢC
 │
 └── G3  Mở rộng 148 mục    8–12 tuần     (2–3 người)
```

### Giai đoạn 0 — Chốt nền tảng (2–3 tuần, ~10–15 ngày công của Vexim + 3–5 ngày BA)

| # | Việc | Chủ sở hữu | Kết quả bàn giao |
| --- | --- | --- | --- |
| 0.1 | Hỏi Cục BVTV / Phòng ATTP&MT: mẫu **2-CV953 (13/4/2022)** và **mẫu 13** còn hiệu lực sau Lệnh 280 (1/6/2026) không? | Vexim | Xác nhận bằng văn bản + file mẫu mới (nếu có) |
| 0.2 | Rà lại bản đồ 40 ↔ 148 mục (tôi đã làm nháp ở Phụ lục G) | Chuyên viên Vexim | File ánh xạ đã duyệt |
| 0.3 | Lập **Bảng 1** (chứng nhận được chấp nhận) và **Bảng 5** (mẫu biểu bắt buộc) — dễ, làm ngay | Chuyên viên | 2 file cấu hình |
| 0.4 | Bắt đầu **Bảng 2** (giới hạn/chỉ tiêu theo mặt hàng) và **Bảng 3** (dịch hại TQ) — khó, cần nguồn chính thức | Chuyên viên + nguồn Cục | Nháp, hoàn thiện trong G1 |
| 0.5 | Chuẩn bị **20–30 bộ hồ sơ đã ẩn danh** (hoặc xin phép 2–3 khách) | Vexim | Golden set |
| 0.6 | Chốt nhà cung cấp AI + điều khoản dữ liệu (không huấn luyện trên dữ liệu khách, khu vực xử lý) + DPIA/CTIA | Pháp chế + IT | Điều kiện dùng dữ liệu thật |
| 0.7 | Hồ sơ tự phân loại rủi ro AI theo NĐ 142/2026 (nhiều khả năng: **rủi ro trung bình** → cần ghi nhãn đầu ra AI) | Sản phẩm + pháp chế | Hồ sơ phân loại |

**Chi phí G0:** chủ yếu nội bộ. Nếu thuê BA hỗ trợ: ~1–3 nghìn USD.

### Giai đoạn 1 — PoC đo chất lượng (4–6 tuần, 1–2 dev)

**Không viết giao diện.** Chạy script trên golden set để trả lời: *với hồ sơ thật của Vexim, máy làm được tới đâu?*

| Việc | Kết quả |
| --- | --- |
| Thử 2–3 engine OCR/đọc text trên 20–30 bộ | Chọn được engine theo số đo, không theo cảm giác |
| Phân loại 40 mã mục (tập đóng) | Ma trận nhầm lẫn: cặp mã nào hay bị nhầm (vd. 6.1.1 ↔ A3) |
| Gợi ý "tài liệu này thuộc mã nào" | Tỷ lệ gợi ý đúng |
| Đo thời gian & chi phí/bộ | Số liệu thật để chốt giá |

**Ngưỡng Go/No-Go (đo trên tập giữ lại, không dùng để tinh chỉnh):**

| Chỉ số | Ngưỡng tiếp tục |
| --- | --- |
| Phân loại tài liệu đúng (top-1) | ≥ **85%** |
| **Không bỏ sót** (có tài liệu nhưng máy không tìm thấy) | ≥ **90%** |
| Tỷ lệ gợi ý mã mục đúng | ≥ **75%** |
| Số ca máy tự sinh giá trị/kết luận | **= 0** (bất biến) |
| Chi phí xử lý/bộ | ≤ **5 USD** |
| Thời gian 1 bộ 100 file | ≤ **2 giờ** |

**Chi phí G1:** 2–3 person-month ≈ **6–12 nghìn USD**.
**Kết luận sau G1:** nếu đạt → sang G2. Nếu không đạt ≥2 chỉ số → dừng, chỉ giữ lại phần "OCR + đặt tên file" (vẫn có giá trị) và đánh giá lại sau.

### Giai đoạn 2 — Công cụ mức 1 (10–14 tuần, 2 người)

**Có (scope đóng):**

1. Tạo case: tên DN · cơ sở · sản phẩm đăng ký · **thuộc tính điều kiện** (có kho lạnh? có phụ gia? có hun trùng? có hơi nước tiếp xúc? tự làm hay thuê kiểm soát côn trùng? phòng TN nội bộ hay thuê?) → 9 nhánh điều kiện tự động bật/tắt.
2. Tải lên hàng loạt: lưu bản gốc + checksum + phiên bản + người tải + ngày.
3. OCR/đọc text → phân loại → **tự gắn mã mục** (nhiều mã cho một tệp).
4. **Tự sinh BẢNG KÊ TÀI LIỆU** đúng format tài liệu Vexim (cột: STT / tên tệp / mã mục / ghi chú-ngày / độ tin cậy / xác nhận).
5. **Ma trận 40 mục**: mỗi mục 3 trạng thái `Đã có / Cần bổ sung / Không áp dụng (+ lý do)`, kèm danh sách tệp ứng viên để bấm xem ngay.
6. Tự động: kiểm tra ngày hết hạn chứng chỉ (A4, 11.2, 8.3) · **đối chiếu chéo tên/địa chỉ/người đại diện** giữa GPKD ↔ biểu mẫu ↔ bản tuyên bố · kiểm tra độ phủ thời gian của nhật ký (12.3, 8.3, 7.1).
7. Cảnh báo "1 tệp đang gắn cho N mục" và "mục này chưa có tệp nào".
8. Ghi nhãn rõ `AI đề xuất` / `Chuyên viên xác nhận` trên từng ô (phục vụ NĐ 142).
9. Export Excel đúng mẫu Vexim + disclaimer + phiên bản checklist + ngày lập.
10. Phân quyền theo khách hàng · audit log (ai xem/tải/sửa/export).

**Không có:** tự đánh giá nội dung · tự soạn tài liệu · cổng khách hàng · workflow duyệt nhiều cấp · tích hợp CRM/Drive · export Word · bản 148 mục (để G3).

**Chi phí G2:** 5–7 person-month ≈ **15–30 nghìn USD**.

### Giai đoạn 3 — Mở rộng (8–12 tuần, 2–3 người)

- Nhập 148 mục làm lớp kiểm tra bên trong, ánh xạ với 40 mục.
- Evidence cấp nội dung có trích dẫn (trang + đoạn trích + chốt kiểm tra quote-match).
- Yêu cầu bổ sung gửi khách (có mã mục + minh chứng gợi ý).
- Lịch sử thay đổi · so sánh phiên bản checklist.
- Tùy chọn: cổng khách hàng để DN tự tải và theo dõi.

**Chi phí G3:** 8–12 person-month ≈ **25–50 nghìn USD**.

---

## 3. Nhân sự và chi phí tổng

| Giai đoạn | Đội | Person-month | Chi phí phần mềm | Thời gian chuyên gia Vexim |
| --- | --- | --- | --- | --- |
| G0 | 0,5 BA + Vexim | – | ~1–3 nghìn USD (nếu thuê) | **10–15 ngày công** |
| G1 | 1–2 dev | 2–3 | 6–12 nghìn USD | 3–5 ngày (gán nhãn) |
| G2 | 1 BE + 1 FE (+0,5 BA) | 5–7 | 15–30 nghìn USD | 5–8 ngày (nội dung + UAT) |
| **Tổng đến khi dùng được** | | **7–10** | **~22–45 nghìn USD** | **~20–30 ngày công** |
| G3 | 2–3 người | 8–12 | 25–50 nghìn USD | 10–15 ngày |
| **Tổng toàn bộ** | | 15–22 | **~47–95 nghìn USD** | ~30–45 ngày công |

*Đơn giá giả định 2.500–4.500 USD/person-month. Sai số ±50% — dev phải báo giá chính thức sau G0.*

**Hạ tầng & AI:** ~100–400 USD/tháng ở quy mô này (OCR ~1,5–10 USD/1.000 trang; LLM vài USD/case). **Không đáng kể** — đừng tối ưu ở đây.

**So sánh:** đặc tả gốc (nền tảng đầy đủ) ước lượng 80–220 nghìn USD. Kế hoạch này đạt được ~70% giá trị thực tế với ~30% chi phí.

---

## 4. Rủi ro và ngưỡng dừng

| Rủi ro | Mức | Biện pháp | Dấu hiệu phải dừng/đổi hướng |
| --- | --- | --- | --- |
| Mẫu A2 / mẫu 13 đã lỗi thời | **Cao** | Chốt ở G0 bằng văn bản | Cục trả lời không rõ → hoãn phần kiểm tra mẫu, vẫn làm phần còn lại |
| Scan quá kém, OCR không dùng được | **Cao** | Đo ở G1; ngưỡng "trang dùng được" ≥85% | < 70% → chỉ giữ OCR + đặt tên file, không làm phân loại |
| Máy tự sinh giá trị/kết luận | **Cao** | Bất biến R2 + test tự động | Bất kỳ 1 ca nào phát hiện sau G1 → chặn phát hành |
| Bảng tham chiếu (giới hạn, dịch hại) không có | **Cao** | 9 mục 🔶 chỉ hiện "cần chuyên viên đối chiếu", không hiện số | Chuyên gia không có nguồn chính thức → những mục này luôn là việc người |
| Chuyên viên "tick hàng loạt" không đọc | **Cao** | Bắt buộc xác nhận từng mục mới tạo; báo cáo sampling | Tỷ lệ sửa < 2% (nghĩa là không ai đọc thật) → xem lại quy trình |
| Lộ dữ liệu giữa khách hàng | Trung bình | Phân quyền backend + test chéo tenant trước go-live | – |
| Chi phí vượt do retry vòng lặp | Trung bình | Hạn mức AI theo case/ngày | – |
| Danh mục GACC thay đổi giữa chừng | Trung bình | Checklist có phiên bản; khi đổi → phiên bản mới, không sửa hồi tố | – |

**Ngưỡng dừng hẳn dự án:** (1) không có người chịu trách nhiệm nội dung checklist; (2) không có bộ hồ sơ thật để đo; (3) chuyên viên từ chối quy trình "xác nhận từng mục".

---

## 5. Việc cần làm trong 7 ngày tới

| # | Việc | Ai | Xong khi |
| --- | --- | --- | --- |
| 1 | Gửi Cục BVTV/Phòng ATTP&MT câu hỏi về **mẫu 2-CV953 (13/4/2022)** và **mẫu 13** | Vexim | Ngày 3 |
| 2 | Chỉ định **1 người chịu trách nhiệm nội dung checklist** (tên cụ thể + số ngày/tuần) | Ban dự án | Ngày 1 |
| 3 | Rà lại bản đồ 40 ↔ 148 mục ở Phụ lục G, sửa chỗ nào sai | Chuyên viên | Ngày 5 |
| 4 | Lập **Bảng 1** (chứng nhận được chấp nhận, ~10 dòng) | Chuyên viên | Ngày 3 |
| 5 | Liệt kê 20–30 bộ hồ sơ có thể dùng đo thử + cách ẩn danh | Vexim | Ngày 5 |
| 6 | Chốt danh sách nhà cung cấp AI được phép (điều khoản dữ liệu) | Pháp chế + IT | Ngày 7 |
| 7 | Quyết định: làm G1 (PoC 4–6 tuần, ~6–12 nghìn USD) hay dừng ở mức “OCR + Excel thủ công” | Vexim | Ngày 7 |

---

## 6. Tài liệu liên quan

| Tài liệu | Dùng khi |
| --- | --- |
| [`QUYET_DINH_MUC_TOI_THIEU.md`](QUYET_DINH_MUC_TOI_THIEU.md) | Cần giải thích với nội bộ/ban lãnh đạo vì sao thu hẹp phạm vi |
| [`BAO_CAO_KHA_THI.md`](BAO_CAO_KHA_THI.md) | Cần đánh giá tổng thể, pháp lý, chi tiết kỹ thuật |
| [`phu-luc/G_phan_loai_40_muc_CucBVTV.md`](phu-luc/G_phan_loai_40_muc_CucBVTV.md) | **Bảng 40 mục theo tài liệu Cục BVTV** — dùng cho G2 |
| [`phu-luc/F_phan_loai_148_muc_checklist.md`](phu-luc/F_phan_loai_148_muc_checklist.md) | Bảng 148 mục chi tiết — dùng cho G3 |
| [`phu-luc/E_ke_hoach_danh_gia_chat_luong_AI.md`](phu-luc/E_ke_hoach_danh_gia_chat_luong_AI.md) | Chạy G1: golden set, chỉ số, cách chấm |
| [`phu-luc/B_mo_hinh_chi_phi.csv`](phu-luc/B_mo_hinh_chi_phi.csv) | Mô hình chi phí, chỉnh giả định để tự tính |
