# checklist_GACC

Kho tài liệu phục vụ việc **đánh giá tính khả thi** và thiết kế nền tảng AI hỗ trợ rà soát hồ sơ đa sản phẩm
(thị trường × chương trình/quy định × nhóm hàng × loại cơ sở × phiên bản yêu cầu) cho Vexim.

> ⚠️ Tài liệu trong kho này là **đánh giá sơ bộ để lập kế hoạch**, không phải tư vấn pháp lý và **không phải**
> nội dung nghiệp vụ đã được phê duyệt. Mọi yêu cầu quy định phải được Vexim/chuyên gia xác minh với văn bản
> gốc trước khi dùng.

## Tài liệu chính

- **[Kế hoạch triển khai](docs/KE_HOACH_TRIEN_KHAI.md)** — **bắt đầu từ đây**: 5 quy tắc bất di bất dịch, MVP =
  tự động điền BẢNG KÊ TÀI LIỆU, lộ trình 4 giai đoạn (G0→G3), ngân sách, ngưỡng dừng, việc làm trong 7 ngày.
- **[Báo cáo nghiên cứu tính khả thi](docs/BAO_CAO_KHA_THI.md)** — kết luận, điểm khả thi theo 9 trục, rủi ro,
  kiến trúc đề xuất, ước lượng chi phí, lộ trình và ngưỡng Go/No-Go.
- **[Quyết định phạm vi tối thiểu](docs/QUYET_DINH_MUC_TOI_THIEU.md)** — thu hẹp xuống đúng nhu cầu thực tế:
  *AI chỉ soi thiếu, không kết luận*; chia checklist 2 tầng; thiết kế lệch về lỗi an toàn; 3 phương án
  (không xây / công cụ nhẹ / nền tảng đầy đủ) kèm nỗ lực & chi phí.

## Phụ lục

| Phụ lục | Nội dung |
| --- | --- |
| [A](docs/phu-luc/A_bo_cau_hoi_vexim_va_de_xuat_tra_loi.md) | 12 câu hỏi cần Vexim chốt + **đáp án đề xuất** để chốt phạm vi nhanh |
| [B](docs/phu-luc/B_mo_hinh_chi_phi.csv) | Mô hình chi phí: 3 kịch bản khối lượng, OPEX/CAPEX, công thức |
| [C](docs/phu-luc/C_mau_requirement_pack.yaml) | Mẫu cấu trúc requirement pack (minh hoạ cấu hình — **chưa xác minh**) |
| [D](docs/phu-luc/D_dang_ky_nguon_tham_chieu.md) | Đăng ký nguồn tham chiếu, phân loại độ tin cậy A/B/C, quy tắc quản trị nguồn |
| [E](docs/phu-luc/E_ke_hoach_danh_gia_chat_luong_AI.md) | Kế hoạch đo chất lượng AI (golden set, chỉ số, ngưỡng, test bất biến) |
| [F](docs/phu-luc/F_phan_loai_148_muc_checklist.md) | **Phân loại 148 mục checklist ngũ cốc**: T1/T2/T3, 13 mục cần dữ liệu tham chiếu, 7 phát hiện, thứ tự ưu tiên làm |
| [F-dữ liệu](docs/phu-luc/F_du_lieu_phan_loai.csv) | Dữ liệu thô của bảng phân loại (mở bằng Excel để tự chỉnh/sửa) |

## Ba điểm cần nhớ

1. **Lệnh 248 đã hết hiệu lực** (thay bởi Lệnh 280, có hiệu lực 1/6/2026). Bất kỳ checklist/pack nào xây trên
   Lệnh 248 đều cần biên soạn lại trước khi dùng.
2. Danh mục hàng hóa phải "đề xuất chính thức" nay là **danh mục động** (hiện 17 nhóm, gồm ngũ cốc dùng làm
   thực phẩm) và được tinh chỉnh theo sản phẩm — ví dụ nhóm gạo có các yếu tố rủi ro riêng.
3. Rủi ro lớn nhất **không nằm ở AI**: đó là nội dung quy định thay đổi, chất lượng bản scan, và việc thiếu
   chủ sở hữu nội dung (người biên soạn/kiểm tra/duyệt requirement pack).
