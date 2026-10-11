# Chọn công nghệ và AI — khuyến nghị cụ thể cho dự án này

**Ngày:** 11/10/2026 · **Áp dụng cho:** G1 (PoC) và G2 (công cụ mức 1)
**Lưu ý:** tên mô hình và giá thay đổi vài tháng một lần. Tài liệu này khóa **kiến trúc và tiêu chí chọn**,
còn tên cụ thể phải được xác nhận lại bằng phép đo ở G1 trước khi code.

---

## 0. Năm nguyên tắc chọn

1. **Đừng chọn theo tên mô hình — chọn theo phép đo.** Chạy cùng một golden set trên 3–5 ứng viên rồi chọn (Phụ lục E).
2. **Tách lớp mô hình khỏi ứng dụng.** Mọi kết quả lưu kèm `model_id` + `prompt_version` + `config_version` để đổi mô hình không phải sửa nghiệp vụ và vẫn tái hiện được kết quả cũ.
3. **Giữ tài liệu gốc ở nơi Vexim kiểm soát.** Ra khỏi hệ thống chỉ là những đoạn đã tối giản và đã che dữ liệu cá nhân.
4. **Pipeline tất định, không phải agent tự do.** Classify → extract → retrieve → cite → validate. Agent tự do nhân chi phí lên và khó đoán.
5. **Ít thành phần vận hành.** Đội 2 người không gánh nổi 10 service.

---

## 1. Cấu hình khuyến nghị (nếu chỉ được chọn một)

```
Trình duyệt (React)                         ← tiếng Việt, xem song song PDF & text
        │
FastAPI (Python)  ·  Phân quyền theo khách hàng  ·  Audit log
        │
┌───────┴──────────────────────────────────────────────────────┐
│ 1. NẠP & TRÍCH XUẤT                                          │
│    PDF text  → PyMuPDF / pdfplumber (giữ page + bbox)        │
│    PDF scan  → PaddleOCR-VL (tự host, 1 GPU)  HOẶC managed    │
│    DOCX/XLSX → python-docx / openpyxl                        │
│    Chuẩn hóa → Docling hoặc MinerU (markdown + trang + bbox) │
│    Chất lượng → gắn điểm OCR/trang, dưới ngưỡng → xử lý tay  │
├──────────────────────────────────────────────────────────────┤
│ 2. CHE DỮ LIỆU CÁ NHÂN                                       │
│    Presidio + regex VN (CCCD 12 số, SĐT, email)              │
│    + danh sách nhân sự trích từ chính hồ sơ                  │
├──────────────────────────────────────────────────────────────┤
│ 3. PHÂN LOẠI & GẮN MÃ MỤC (tập đóng 40/148 mục)              │
│    LLM hạng hiệu quả + danh sách mã + vài ví dụ              │
│    (bổ sung BGE-M3 + pgvector để gợi ý top-3 khi đã có nhãn) │
├──────────────────────────────────────────────────────────────┤
│ 4. TRUY XUẤT BẰNG CHỨNG                                      │
│    Chia đoạn theo trang → embedding BGE-M3 (đa ngôn ngữ)     │
│    → pgvector (HNSW) → reranker đa ngôn ngữ → top-k          │
├──────────────────────────────────────────────────────────────┤
│ 5. SUY LUẬN (2 tầng, rót việc)                               │
│    Tầng nhanh/rẻ: phân loại, trích trường, gán mã            │
│    Tầng mạnh    : mâu thuẫn, câu hỏi mơ hồ, tổng hợp          │
├──────────────────────────────────────────────────────────────┤
│ 6. VALIDATOR TRÍCH DẪN (không phải AI)                       │
│    Bắt buộc locator (trang + span)                           │
│    Kiểm tra quote có nằm trong text trang (fuzzy ≥ 0,9)       │
│    Không khớp → HỦY evidence, ghi log                        │
├──────────────────────────────────────────────────────────────┤
│ 7. XUẤT: openpyxl (Excel) · WeasyPrint/Jinja (PDF)           │
└──────────────────────────────────────────────────────────────┘
PostgreSQL + pgvector  ·  MinIO (S3-compatible)  ·  Redis + Celery
Docker Compose trên 1–2 máy chủ đặt tại Việt Nam
```

---

## 2. Bảng chọn từng lớp

| Lớp | **Khuyến nghị** | Vì sao | Thay thế được | Chi phí |
|---|---|---|---|---|
| **Ngôn ngữ/khung** | **Python 3.12 + FastAPI** | Toàn bộ hệ sinh thái OCR/ML là Python | Node/TS nếu đội mạnh TS hơn | – |
| **Cơ sở dữ liệu** | **PostgreSQL 16 + pgvector** | Quan hệ (case/tài liệu/trạng thái) + vector trong một chỗ; không cần DB riêng | Qdrant/Weaviate (chỉ khi vượt vài triệu đoạn) | ~30–80 USD/tháng |
| **Kho file** | **MinIO tự host** hoặc object storage VN (FPT/Viettel/VNG) | Bản gốc + checksum + phiên bản; chủ động nơi lưu | AWS S3 / Cloudflare R2 | ~10–50 USD/tháng |
| **Hàng đợi việc** | **Celery + Redis** (hoặc RQ) | Xử lý bất đồng bộ, retry, theo dõi tiến độ — bắt buộc vì 1 case có 50–120 file | Arq / Dramatiq / pgqueuer | cùng máy chủ |
| **Đọc PDF có text** | **PyMuPDF** (nhanh, giữ bbox) + pdfplumber (bảng) | Lấy được tọa độ để highlight trích dẫn | pypdf | OSS |
| **OCR scan/ảnh** | **PaddleOCR-VL tự host (1 GPU)** — mạnh tiếng Trung & Việt, rất nhỏ (~0,9B) | Dữ liệu không ra khỏi VN; chi phí biến đổi ~0 | Managed: Azure DI / Google DAI / Mistral OCR 3 (~1,5–10 USD/1.000 trang) | GPU 200–600 USD/tháng **hoặc** 10–60 USD/tháng |
| **Chuẩn hóa đầu ra** | **Docling** (MIT) hoặc **MinerU** | Ra markdown + trang + bbox đồng nhất cho mọi định dạng | Tự viết (không nên) | OSS — *kiểm tra license MinerU (AGPL) nếu sau này cung cấp dịch vụ cho bên thứ ba* |
| **DOCX / XLSX** | python-docx / openpyxl + pandas | Đọc bảng thiết bị, nhật ký, danh sách NCC | – | OSS |
| **Che dữ liệu cá nhân** | **Microsoft Presidio** + regex VN + danh sách nhân sự | Đáp ứng mục 10.1 của checklist & Luật BVDLCN 91/2025 | Regel thuần + từ điển | OSS |
| **Embedding / truy xuất** | **BGE-M3** (đa ngôn ngữ, gồm VI & ZH) | Một mô hình cho cả 3 thứ tiếng; hỗ trợ cả sparse+dense | multilingual-E5; hoặc dùng embedding của hãng LLM | OSS hoặc vài USD/tháng |
| **Rerank** | Reranker đa ngôn ngữ (BGE reranker hoặc cross-encoder tương đương) | Tăng độ đúng của "tài liệu này thuộc mã nào" rõ rệt so với chỉ dùng vector | Bỏ qua ở G1, thêm ở G2 | OSS |
| **LLM — tầng nhanh** | Một mô hình **hạng hiệu quả** của hãng lớn (có DPA, cam kết không huấn luyện trên dữ liệu khách) | Phân loại / trích trường / gán mã — chiếm ~80% số lần gọi | Mô hình mở tự host | ~10–50 USD/tháng |
| **LLM — tầng mạnh** | Một mô hình **hạng mạnh nhất** trong ngân sách | Chỉ dùng cho ~10–20% ca khó: mâu thuẫn, mơ hồ, tổng hợp | – | ~20–150 USD/tháng |
| **Validator trích dẫn** | **Code Python thuần** (fuzzy match quote ↔ text trang) | Không phải AI. Đây là chốt chặn chống trích dẫn bịa | – | 0 |
| **Giao diện** | **React/Next.js + TanStack Table** (hoặc AG Grid) + **pdf.js** | Bảng 40 mục và BẢNG KÊ là 2 bảng lớn; cần highlight đoạn trích trên ảnh trang | Vue | OSS |
| **Xuất file** | openpyxl (Excel) · WeasyPrint + Jinja (PDF) | Excel là đầu ra chính; PDF kèm disclaimer | docxtpl nếu cần Word | OSS |
| **Đánh giá chất lượng** | Script Python + golden set + chấm mù; LLM-as-judge **chỉ** cho phần gợi ý, có chốt kiểm tra | Đo lại sau mỗi lần đổi prompt/mô hình | – | OSS |
| **Triển khai** | **Docker Compose** trên 1–2 VM tại VN; backup hàng ngày | Đội nhỏ, ít dịch vụ | Kubernetes — **không nên** ở giai đoạn này | ~150–400 USD/tháng |

**Tổng hạ tầng ước tính: 250–1.000 USD/tháng.** Không đáng kể so với nhân sự — đừng tối ưu ở đây.

---

## 3. Ba kịch bản theo ràng buộc dữ liệu

| | **A — Managed** | **B — Hybrid ⭐ khuyến nghị** | **C — Tự host hoàn toàn** |
|---|---|---|---|
| OCR/parse | API nước ngoài | **Tại chỗ / trong nước** | Tại chỗ |
| Lưu file & DB | Cloud nước ngoài | **Tại VN** | Tại chỗ |
| LLM | API nước ngoài | API nước ngoài, **chỉ nhận đoạn đã tối giản + đã che PII** | Mô hình mở trên GPU tại chỗ |
| Ưu | Lên nhanh nhất, ít vận hành | **Cân bằng tốc độ & chủ quyền dữ liệu** | Tối đa kiểm soát |
| Nhược | Hồ sơ gốc (nhiều dữ liệu cá nhân) ra nước ngoài ngay từ đầu | Vận hành 2 phần | Chậm hơn, chất lượng thấp hơn, cần thêm người lo GPU |
| Nghĩa vụ pháp lý | DPA + CTIA + DPIA đầy đủ **ngay từ đầu** | DPA + CTIA (phạm vi hẹp hơn nhiều) + DPIA | Chủ yếu DPIA nội bộ |
| Phù hợp khi | Vexim đã chấp thuận gửi dữ liệu ra ngoài | **Mặc định** | Vexim có quy định nội bộ cấm gửi dữ liệu ra ngoài |

**Lý do chọn B:** tài liệu gốc chứa dữ liệu cá nhân (người đại diện, danh sách nhân sự, hồ sơ khám sức khỏe, chữ ký). Giữ khâu OCR và lưu trữ trong nước nghĩa là dữ liệu nhạy cảm nhất **không bao giờ** rời khỏi VN; ra ngoài chỉ là những đoạn văn bản kỹ thuật đã che thông tin.

---

## 3b. Tóm tắt: nếu chỉ được chọn một cấu hình

| Hạng mục | Chọn |
|---|---|
| Backend | Python + FastAPI |
| Dữ liệu | PostgreSQL + pgvector + MinIO |
| Việc nền | Celery + Redis |
| Đọc PDF | PyMuPDF / pdfplumber |
| OCR scan | **PaddleOCR-VL tự host** (kiểm chứng với 1–2 managed ở G1) |
| Chuẩn hóa | Docling |
| Che PII | Presidio + regex VN |
| Embedding | BGE-M3 |
| LLM | **2 tầng**: 1 hạng hiệu quả (80% việc) + 1 hạng mạnh (20% ca khó) — chọn bằng phép đo ở G1 |
| Validator trích dẫn | Code Python (fuzzy match) |
| Giao diện | React + TanStack Table + pdf.js |
| Triển khai | Docker Compose, 1–2 VM tại VN (kịch bản B — hybrid) |

---

## 4. Chọn mô hình cụ thể thế nào (đừng đoán)

Tên mô hình thay đổi liên tục. Quy trình bắt buộc ở **G1**:

1. **Lập danh sách 4–5 ứng viên**: 1 hạng mạnh · 2 hạng trung bình · 1 hạng rẻ · 1 mô hình mở (nếu tính tự host).
2. **Chạy cùng golden set** (20–30 bộ, chia 70/30), chấm theo Phụ lục E — riêng 3 tập con: tiếng Việt / tiếng Anh / tiếng Trung.
3. **Chọn theo 3 tiêu chí**: chất lượng trên nhóm T2 · chi phí trên 1 bộ · độ ổn định của API & điều khoản dữ liệu.
4. **Khóa version**, ghi vào cấu hình. Đánh giá lại mỗi quý hoặc khi đổi provider.

**Hai lưu ý riêng của dự án này:**
- **Tài liệu tiếng Trung:** chất lượng chênh lệch lớn giữa các mô hình. Phải có tập đánh giá tiếng Trung riêng, không dùng điểm chung.
- **Bảng và nhật ký** (danh sách thiết bị, nhật ký côn trùng, phiếu kiểm nghiệm): đây là phần hay sai nhất. Đo riêng khả năng trích đúng ô/cột, không gộp vào điểm chung.

---

## 5. Những thứ KHÔNG nên dùng ở giai đoạn đầu

| Tránh | Vì |
|---|---|
| Agent framework tự do (nhiều bước tự quyết) | Chi phí nhân lên, kết quả khó đoán, khó giải thích cho chuyên viên. Quy trình ở đây cố định → dùng pipeline |
| Vector DB riêng (Milvus/Weaviate/Qdrant) | Ở quy mô vài chục nghìn đoạn, pgvector đủ và không tăng gánh vận hành |
| Fine-tuning mô hình | Chưa có đủ dữ liệu gán nhãn. Làm prompt + truy xuất + luật trước; fine-tune chỉ khi đã có vài nghìn mẫu và đo được lợi ích |
| Nền tảng RAG đóng gói | Khó kiểm soát trích dẫn ở mức trang/đoạn — mà đây là yêu cầu sống còn |
| Tự xây OCR | Đã có sẵn những mô hình tốt hơn nhiều |
| Kubernetes, microservices | Đội 2 người; 1–2 VM + Docker Compose là đủ |
| Tự động dịch toàn bộ hồ sơ | Tốn kém, và bản dịch không được làm căn cứ. Chỉ dịch để đọc, luôn hiển thị kèm bản gốc |

---

## 6. Ba chỗ cần quyết định **ngay** (ảnh hưởng kiến trúc)

| # | Quyết định | Vì ảnh hưởng |
|---|---|---|
| 1 | **Có được phép gửi dữ liệu ra ngoài không?** (kịch bản A / B / C) | Quyết định có mua GPU hay không, và khối lượng thủ tục pháp lý |
| 2 | **Tự host OCR hay dùng API?** | ~200–600 USD/tháng GPU vs ~10–60 USD/tháng API, đổi lấy chủ quyền dữ liệu |
| 3 | **Lưu file trên hệ thống hay chỉ lập chỉ mục?** | Nếu chỉ lập chỉ mục thì không đảm bảo được "đúng phiên bản tại thời điểm rà soát" — khuyến nghị là lưu |

---

## 7. Dự phòng khi nhà cung cấp đổi giá/điều khoản

- Mọi lời gọi mô hình đi qua **một lớp abstraction** (`ModelProvider`), cấu hình bằng file — đổi provider là đổi cấu hình.
- Mọi kết quả lưu `model_id` + `prompt_version` → đổi mô hình không mất khả năng giải thích kết quả cũ.
- Giữ sẵn ít nhất **2 provider đã kiểm thử** (đã chạy qua golden set) để có chỗ chuyển trong 1 ngày.
- Giới hạn chi phí theo case/ngày + cảnh báo, để một vòng retry lỗi không hóa thành hóa đơn lớn.
