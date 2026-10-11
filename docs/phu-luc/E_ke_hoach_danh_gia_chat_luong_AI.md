# Phụ lục E — Kế hoạch đo chất lượng AI (Eval Plan) cho Giai đoạn 0

**Mục tiêu:** trả lời câu hỏi duy nhất quyết định có nên xây full MVP hay không:
> *Với hồ sơ thật của Vexim, AI có đủ tốt để chuyên viên chỉ cần kiểm chứng thay vì làm lại từ đầu không?*

Thực hiện **trước khi viết code sản phẩm**; có thể chạy trên script + spreadsheet, không cần UI.

---

## E1. Bộ dữ liệu thử nghiệm (golden set)

| Hạng mục | Yêu cầu tối thiểu | Ghi chú |
| --- | --- | --- |
| Số bộ hồ sơ | **20–30 bộ** (tối thiểu 15) | Đã ẩn danh hoặc có đồng ý của khách |
| Phân bố | ≥ 5 bộ tiếng Việt · ≥ 5 bộ có tiếng Anh · ≥ 3 bộ có tiếng Trung | Đúng tỷ lệ thực tế càng tốt |
| Bản scan | ≥ 40% số bộ phải là **scan/xuống cấp** (mờ, nghiêng, con dấu đè) | Đây là phần quyết định rủi ro R4 |
| Độ đa dạng tài liệu | Bao phủ ≥ 12/17 loại tài liệu trong `ai_config.document_types` | |
| Gán nhãn | Chuyên viên gán nhãn: loại tài liệu + 5–10 trường quan trọng + **một phần** evidence theo requirement | Không cần gán nhãn hết mọi thứ |
| Nỗ lực gán nhãn | ~3–5 ngày công chuyên viên cho 20 bộ | Là phần **bắt buộc**, không được bỏ |
| Chia tập | 70% tinh chỉnh (dev) / 30% đánh giá (holdout), **không** nhìn holdout khi tinh chỉnh | |

## E2. Chỉ số, cách tính và ngưỡng

| # | Chỉ số | Cách tính | Ngưỡng tiếp tục | Mục tiêu sau tinh chỉnh |
| --- | --- | --- | --- | --- |
| M1 | Tỷ lệ trang OCR dùng được | Trang đọc đúng ≥ 90% ký tự ở các trường quan trọng / tổng trang | ≥ **85%** | ≥ 92% |
| M2 | Phân loại tài liệu (top-1) | Đúng / tổng tài liệu | ≥ **85%** | ≥ 93% |
| M3 | **Trích dẫn vượt quote-match** | Evidence có trích dẫn khớp chuỗi trong đúng page/locator / tổng evidence sinh ra | ≥ **90%** | ≥ 95% |
| M4 | Tỷ lệ evidence được chuyên viên chấp nhận | Số evidence giữ lại sau kiểm / tổng evidence đề xuất | ≥ **70%** | ≥ 85% |
| M5 | Độ đầy đủ (recall) tìm evidence | Evidence đúng tìm được / tổng evidence có thật trong bộ hồ sơ (theo nhãn) | ≥ **60%** | ≥ 80% |
| M6 | Tỷ lệ gợi ý trạng thái được giữ nguyên | Số gợi ý không bị sửa / tổng gợi ý | ≥ **60%** | ≥ 75% |
| M7 | **Số ca AI tự khẳng định thiếu căn cứ** | Đếm thủ công + test tự động | **= 0** | **= 0** (bất biến) |
| M8 | Thời gian xử lý case 100 file | Đo end-to-end | ≤ **4 giờ** | ≤ 1 giờ |
| M9 | Chi phí AI/case | Token + OCR thực tế | ≤ **15 USD** | ≤ 5 USD |
| M10 | Tỷ lệ trường bị "bịa" (fabricated value) | Trường có giá trị nhưng không tồn tại trong nguồn / tổng trường trích xuất | **= 0** | **= 0** (bất biến) |

**Quy tắc đánh giá:** đạt **tất cả** ngưỡng "tiếp tục" ⇒ Go. Không đạt ≥2 chỉ số ⇒ No-Go/đổi hướng (xem 8.2 báo cáo chính).

## E3. Cách chấm (tránh tự lừa mình)

1. **Chấm mù (blind):** người chấm không biết kết quả đến từ engine nào (đang so sánh 2–3 engine OCR/LLM).
2. **Chấm theo từng evidence, không theo cảm giác chung:** mỗi evidence có 3 nút: *Chấp nhận / Sửa / Bỏ*. Ghi lý do khi Bỏ.
3. **Đo lại sau mỗi thay đổi:** prompt, model, ngưỡng retrieval — mỗi lần chạy lưu `prompt_version`, `model_id`, `retrieval_config_version` (đúng mục 10 đặc tả).
4. **Không tinh chỉnh trên holdout:** chỉ chấm holdout 1–2 lần ở cuối.
5. **Lưu mọi kết quả thô** (JSON theo "hợp đồng đầu ra AI" mục 10) để đối soát sau.

## E4. Phân loại lỗi (dùng để ưu tiên sửa)

| Nhóm lỗi | Ví dụ | Ưu tiên | Hướng xử lý |
| --- | --- | --- | --- |
| **Lỗi nghiêm trọng** | Tự kết luận "đạt/đủ điều kiện"; tự điền số liệu/ngày; trích dẫn bịa | P0 — chặn phát hành | Chốt kiểm soát C1–C6 (mục 4.3) + test bất biến |
| **Sai nội dung** | Nhầm tên cơ sở, nhầm mã số, nhầm đơn vị | P0 | Thêm quy tắc hậu kiểm (so khớp danh mục, regex mã) |
| **Sai định vị** | Đúng nội dung nhưng sai trang/sai đoạn | P1 | Validator locator; bắt buộc span offset |
| **Thiếu** | Có bằng chứng trong hồ sơ nhưng không tìm thấy | P1 | Tăng top-k, cải thiện truy xuất, bổ sung từ đồng nghĩa VN/ZH |
| **Thừa** | Đề xuất evidence không liên quan | P2 | Tăng ngưỡng score, lọc theo loại tài liệu |
| **OCR** | Không đọc được | P1 | Chuyển `needs OCR`/xử lý thủ công; thử engine khác |

## E5. Bộ test bất biến (phải có tự động trước go-live)

| Test | Kỳ vọng |
| --- | --- |
| `AI không bao giờ chuyển requirement sang "Đã rà soát nội bộ"/"Đã đưa vào bộ hồ sơ"` | Mọi đường dẫn thay đổi trạng thái có `actor` là người; AI job không có quyền này |
| `Không evidence nào tồn tại mà không có locator` | Ràng buộc DB + kiểm tra ở tầng dịch vụ |
| `Mọi evidence đã lưu đều vượt quote-match` | Chạy lại validator trên toàn bộ evidence mỗi đêm (batch) |
| `Người dùng khách A không truy cập được dữ liệu khách B` (API + tìm kiếm + export) | Test tự động chéo tenant cho từng endpoint |
| `Thiếu thuộc tính điều kiện ⇒ trạng thái "Cần làm rõ"` | Unit test trên rule engine |
| `Không trường nào có giá trị khi nguồn trống` | Test với tài liệu "rỗng" (trang trắng/nhiễu) |
| `Export luôn kèm disclaimer + pack version + ngày lập` | Test snapshot trên file xuất |

## E6. Nhịp đo sau khi có sản phẩm

| Chu kỳ | Việc |
| --- | --- |
| Hàng tuần | Dashboard: tỷ lệ chấp nhận evidence, số ca `needs OCR`, chi phí/case |
| Hàng tháng | Chấm lại mẫu 30 evidence ngẫu nhiên; báo cáo chỉ số M1–M7 |
| Hàng quý | Chạy lại toàn bộ golden set; rà soát nguồn & danh mục GACC |
| Khi đổi model/prompt | Chạy lại E2 trước khi deploy; lưu kết quả để so sánh |

## E7. Kết quả cần bàn giao sau Giai đoạn 0

1. Ma trận so sánh 2–3 engine OCR trên chính dữ liệu Vexim (M1, chi phí, tốc độ).
2. Bảng chỉ số M1–M10 + danh sách lỗi theo E4.
3. Golden set đã gán nhãn (tài sản tích lũy, dùng lại cho mọi pack sau).
4. Khuyến nghị Go/No-Go + phạm vi MVP đã điều chỉnh.
5. Ước lượng chi phí/case thực tế để chốt giá bán/ngân sách.
