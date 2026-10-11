# Phụ lục A — 12 câu hỏi cần Vexim chốt và đáp án đề xuất

**Cách dùng:** cột "Đề xuất" là câu trả lời mặc định mà nhóm kỹ thuật khuyến nghị **nếu Vexim chưa có ý kiến khác**. Vexim chỉ cần xác nhận hoặc ghi đè — giúp chốt phạm vi trong 1 buổi làm việc thay vì nhiều vòng.

---

## Nhóm 1 — Quyết định phạm vi MVP (5 câu, quan trọng nhất)

### C1. Dùng nội bộ Vexim trước hay có cổng khách hàng ngay MVP?

| | |
| --- | --- |
| **Đề xuất** | **Nội bộ trước.** Cổng khách hàng đưa vào Giai đoạn 2 |
| **Lý do** | Cổng khách hàng kéo theo: xác thực ngoài, phân quyền chi tiết, luồng phản hồi, thông báo, kiểm soát chia sẻ liên kết, và rủi ro lộ dữ liệu cao nhất. Trong khi giá trị cốt lõi (rút ngắn thời gian rà soát nội bộ) đạt được mà không cần cổng |
| **Đổi lại** | Khách vẫn nhận yêu cầu bổ sung qua email/Excel xuất tay trong MVP |
| **Cần quyết định trước** | Ngày |

### C2. Một hồ sơ có thường gồm nhiều sản phẩm/cơ sở/thị trường không?

| | |
| --- | --- |
| **Đề xuất** | Thiết kế cho **1 thị trường + 1 pack/case**; cho phép **nhiều sản phẩm** trong case *chỉ khi* cùng phạm vi yêu cầu; **nhiều cơ sở** cho phép nhưng mỗi cơ sở là một nhóm requirement riêng |
| **Lý do** | Đúng với mục 5.1 của đặc tả và với thực tế GACC: mỗi địa điểm cơ sở cần mã riêng, mỗi nhóm hàng cần mã riêng — nên "case = thị trường × pack" là đơn vị tự nhiên |
| **Cần Vexim điền** | Tỷ lệ hồ sơ nhiều sản phẩm; tỷ lệ nhiều cơ sở; danh sách nhóm hàng hay gặp |

### C3. Có cần lưu file khách trên hệ thống hay chỉ lập chỉ mục (kho hiện có)?

| | |
| --- | --- |
| **Đề xuất** | **Lưu trên hệ thống** (object storage) ở MVP, kèm checksum và phiên bản |
| **Lý do** | Trích dẫn cần gắn với **đúng phiên bản file tại thời điểm rà soát**; nếu chỉ lập chỉ mục kho ngoài, không đảm bảo được điều này và audit sẽ yếu. Chi phí lưu trữ ở quy mô Vexim rất nhỏ so với chi phí nhân sự |
| **Điều kiện** | Cần chốt chính sách lưu trữ/xóa theo loại tài liệu (xem C10) |
| **Phương án thay thế** | Nếu Vexim đã có DMS/SharePoint bắt buộc: hệ thống lưu **bản sao bất biến + checksum** còn bản chính để ở kho cũ |

### C4. Có giới hạn dữ liệu/nhà cung cấp AI nào theo hợp đồng hoặc chính sách Vexim?

| | |
| --- | --- |
| **Đề xuất** | Chọn **phương án Hybrid**: OCR/parse ưu tiên xử lý tại chỗ hoặc trong khu vực; LLM dùng nhà cung cấp có **điều khoản không huấn luyện trên dữ liệu khách**, có DPA, và **tối giản dữ liệu trước khi gửi** (chỉ gửi đoạn cần thiết, ẩn danh người liên hệ/CCCD/số điện thoại khi không cần cho requirement) |
| **Lý do** | Cân bằng tốc độ triển khai và nghĩa vụ pháp lý; giữ nguyên khả năng chuyển sang self-host sau |
| **Bắt buộc làm trước khi đưa hồ sơ thật vào** | (i) DPA/điều khoản xử lý dữ liệu; (ii) đánh giá tác động chuyển dữ liệu ra nước ngoài; (iii) DPIA |
| **Cần Vexim xác nhận** | Có bị cấm dùng nhà cung cấp cụ thể nào không? Có yêu cầu lưu dữ liệu tại VN không? |

### C5. Đầu ra ưu tiên: Excel, Word/PDF hay cả ba?

| | |
| --- | --- |
| **Đề xuất** | **Excel** (ma trận đối chiếu) là bắt buộc ở MVP; **PDF** cho báo cáo rà soát nội bộ; **Word** để sau |
| **Lý do** | Excel là thứ chuyên viên thao tác tiếp; PDF để lưu/gửi có disclaimer; Word chỉ cần khi có mẫu biểu cố định của Vexim/khách |
| **Cần Vexim cung cấp** | 1 file Excel mẫu + 1 báo cáo mẫu đang dùng để bám theo |

---

## Nhóm 2 — Quyết định kiến trúc dữ liệu & vận hành (4 câu)

### C6. Ai chịu trách nhiệm xác minh và duyệt từng requirement pack?

| | |
| --- | --- |
| **Đề xuất** | Chỉ định **1 người biên soạn** (BA/chuyên viên nghiệp vụ) + **1 người kiểm tra chuyên môn** + **1 người phê duyệt** (quản lý); ghi rõ tên trong pack (đúng mục 5.3) |
| **Lý do** | Đây là nút thắt lớn nhất: thiếu vai trò này thì hệ thống xong mà không có nội dung để chạy |
| **Bổ sung đề xuất** | Chu kỳ rà soát pack: **3 tháng/lần** với pack đang dùng; ngay lập tức khi GACC thay đổi danh mục |

### C7. Người dùng dự kiến và dung lượng hồ sơ trung bình mỗi tháng?

| | |
| --- | --- |
| **Đề xuất (giả định lập kế hoạch)** | 20 người dùng nội bộ; **30 case mới/tháng**; **35 file/case**; **~6 trang/file**; file trung bình 2–5 MB |
| **Lý do** | Quy mô này đủ nhỏ để chi phí hạ tầng không đáng kể, đủ lớn để cần tự động hóa |
| **Cần Vexim điền số thật** | Số case/năm; số file trung bình/case; % bản scan; % tài liệu tiếng Trung; dung lượng trung bình |

### C8. Hệ thống cần tích hợp CRM, email, Drive/SharePoint hoặc phần mềm hiện tại nào?

| | |
| --- | --- |
| **Đề xuất** | **Không tích hợp ở MVP**, trừ **email gửi thông báo/yêu cầu bổ sung** (SMTP đơn giản) |
| **Lý do** | Chưa rõ hệ thống đích; tích hợp là nguồn lớn của chậm tiến độ |
| **Cần Vexim liệt kê** | Hệ thống đang dùng để quản lý khách/hồ sơ (nếu có) để đánh giá ở Giai đoạn 2 |

### C9. Khách có cần đăng nhập, theo dõi tiến độ và tải bổ sung không?

| | |
| --- | --- |
| **Đề xuất** | **Chưa** ở MVP (xem C1). Ở Giai đoạn 2: tài khoản chỉ xem case của mình + tải bổ sung |
| **Lý do** | Tiết kiệm ~15–25% nỗ lực và giảm đáng kể rủi ro bảo mật |
| **Cần Vexim xác nhận** | Có khách nào yêu cầu cổng ngay từ đầu không (ví dụ khách lớn)? |

### C10. Quy định lưu trữ/xóa hồ sơ và thời gian lưu theo loại tài liệu?

| | |
| --- | --- |
| **Đề xuất** | Cấu hình `retention_policy` theo **loại tài liệu**, mặc định: giấy tờ pháp lý & chứng chỉ: **thời hạn hợp đồng + 5 năm** (khớp chu kỳ đăng ký GACC 5 năm); tài liệu lô hàng: **3 năm**; bản nháp/trùng: **6 tháng**; có `legal_hold` để chặn xóa khi đang tranh chấp |
| **Lý do** | Gắn với chu kỳ hiệu lực mã đăng ký 5 năm; tránh lưu vô hạn |
| **Bắt buộc** | Xóa mềm + audit; quy trình xóa thật có duyệt (mục 11) |
| **Cần Vexim chốt** | Thời hạn thật theo hợp đồng & quy định ngành |

---

## Nhóm 3 — Quyết định nội dung & ngôn ngữ (2 câu)

### C11. Có cần hỗ trợ dịch chính thức không, hay chỉ bản nháp để chuyên gia hiệu đính?

| | |
| --- | --- |
| **Đề xuất** | **Chỉ bản nháp** (draft machine translation) để chuyên viên hiệu đính; **bản gốc luôn là căn cứ** và được hiển thị cạnh bản dịch |
| **Lý do** | Dịch máy không thể chịu trách nhiệm pháp lý; nhưng rất hữu ích để đọc nhanh tài liệu tiếng Trung |
| **Bắt buộc** | Ghi nhãn rõ "dịch máy — không dùng làm căn cứ" (cũng phục vụ nghĩa vụ ghi nhãn nội dung AI theo NĐ 142) |
| **Hệ quả** | Tài liệu tiếng Trung: hiển thị song ngữ gốc/dịch + quote ở **bản gốc** |

### C12. Ai phê duyệt khi một case sẵn sàng chuyển cho cơ quan có thẩm quyền?

| | |
| --- | --- |
| **Đề xuất** | Người duyệt được phân quyền (quản lý nghiệp vụ) trong hệ thống; hệ thống **chỉ ghi nhận trạng thái "Sẵn sàng phối hợp nộp"**, không bao giờ tự tuyên bố "đã nộp" |
| **Lý do** | Đúng nguyên tắc mục 7.2; tránh rủi ro pháp lý & uy tín |
| **Cần Vexim chốt** | Tên vai trò thực tế để cấu hình bước cuối |

---

## Tóm tắt quyết định cần ký duyệt

| # | Quyết định | Đề xuất | Ảnh hưởng ước lượng |
| --- | --- | --- | --- |
| 1 | Cổng khách hàng trong MVP | Không | −20% nỗ lực |
| 2 | Đơn vị case | Thị trường × pack | Kiến trúc cốt lõi |
| 3 | Lưu file | Có, tại chỗ/khu vực | Bắt buộc cho truy vết |
| 4 | Nhà cung cấp AI | Hybrid + DPA + tối giản | Điều kiện pháp lý |
| 5 | Export | Excel + PDF | −5% nỗ lực |
| 6 | Chủ sở hữu pack | 1 biên soạn + 1 chuyên môn + 1 duyệt | **Điều kiện sống còn** |
| 7 | Quy mô | 30 case/tháng (giả định) | Hạ tầng |
| 8 | Tích hợp | Chỉ email | −10% nỗ lực |
| 9 | Tài khoản khách | Giai đoạn 2 | −15% nỗ lực |
| 10 | Lưu trữ | Theo loại, mặc định 3–5 năm | Thiết kế DB |
| 11 | Dịch | Bản nháp, gốc là căn cứ | UI song ngữ |
| 12 | Bước cuối | Người duyệt nội bộ | Cấu hình workflow |
