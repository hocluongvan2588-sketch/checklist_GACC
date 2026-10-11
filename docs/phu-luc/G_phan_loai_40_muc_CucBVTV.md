# Phân loại 40 mục — Danh sách kiểm tra hồ sơ theo tài liệu Cục Bảo vệ thực vật

**Nguồn:** *DANH SÁCH KIỂM TRA HỒ SƠ ĐĂNG KÝ CƠ SỞ SẢN XUẤT XUẤT KHẨU THỰC PHẨM SANG TRUNG QUỐC — Danh sách dành cho doanh nghiệp, biên soạn theo tài liệu Cục Bảo vệ thực vật cung cấp*.

Kế hoạch triển khai tương ứng: [`KE_HOACH_TRIEN_KHAI.md`](../KE_HOACH_TRIEN_KHAI.md).  
Bản chi tiết 148 mục (theo biểu mẫu gốc Trung Quốc): [`F_phan_loai_148_muc_checklist.md`](F_phan_loai_148_muc_checklist.md).

Dữ liệu thô: `G_du_lieu_40_muc_CucBVTV.csv`.

## 1. Kết luận nhanh

| Tầng | Số mục | Tỷ lệ | Ý nghĩa |
|---|---:|---:|---|
| **T1 — Máy làm, người tick** | 5 | 12% | Xử lý xong, chuyên viên chỉ xác nhận |
| **T2 — Máy tìm, người quyết** | 32 | **80%** | Máy tìm tài liệu & gắn mã mục; **người quyết mục đó đã đủ chưa** |
| **T3 — Người làm** | 3 | 8% | Máy không đụng vào |
| **Tổng** | **40** | 100% | |

9/40 mục (22%) cần **dữ liệu tham chiếu chuẩn** (🔶), và **9 mục là nhánh có điều kiện** (chỉ yêu cầu nếu doanh nghiệp có hoạt động tương ứng).

> **Điểm khác biệt lớn so với bản 148 mục:** bản này là danh sách **thu thập tài liệu**, không phải danh sách đánh giá sự phù hợp. Vì vậy tỷ lệ T2 lên tới 80% — nghĩa là công việc chủ đạo của hệ thống không phải là *phán xét* mà là **tìm đúng tài liệu và gắn đúng mã mục**. Đó là việc máy làm được, và nó chính là thứ ngốn thời gian của chuyên viên hiện nay.

## 2. Hai checklist: hai mục đích khác nhau, phải giữ cả hai

| | **Bản 40 mục (Cục BVTV)** | **Bản 148 mục (biểu mẫu gốc TQ)** |
|---|---|---|
| Mục đích | Giao tiếp với doanh nghiệp: thu thập đủ giấy tờ | Rà soát nội bộ: đánh giá mức độ phù hợp |
| Độ chi tiết | 1 dòng ≈ 3–5 đầu việc | Tách nhỏ từng đầu việc |
| Người đọc | Doanh nghiệp | Chuyên viên Vexim |
| Trọng tâm | "Có gửi tài liệu này chưa?" | "Nội dung có đáp ứng không?" |
| Hệ thống dùng để | **Mặt tiền**: danh mục yêu cầu, bảng kê tài liệu | **Bên trong**: tiêu chí rà soát chi tiết |

**Sai lầm cần tránh:** nếu chỉ đưa 40 mục vào hệ thống, bạn sẽ bỏ sót 108 đầu việc rà soát. Nếu đưa 148 mục cho doanh nghiệp, doanh nghiệp sẽ ngợp. Thiết kế đúng: **40 mục là lớp giao tiếp, 148 mục là lớp kiểm tra, có ánh xạ giữa hai lớp** (cột "Mã 148" ở bảng dưới).

## 3. Năm phát hiện từ tài liệu này

**G1 — Tài liệu đã viết sẵn 4 quy tắc mà hệ thống phải tuân theo.** Đây là đặc tả hành vi có sẵn, không cần sáng tác:

| Quy tắc (trích từ tài liệu của bạn) | Cài vào hệ thống thế nào |
|---|---|
| "Một tài liệu có thể làm bằng chứng cho nhiều mục. Chỉ cần gửi một lần… từng yêu cầu vẫn được rà riêng" | Cấu trúc many-to-many: 1 tài liệu ↔ N mã mục; **mỗi mã mục có trạng thái riêng** |
| "Đây là danh sách để đối chiếu hồ sơ hiện có; không có nghĩa doanh nghiệp phải làm mới tất cả" | Trạng thái mặc định là "đối chiếu", không phải "làm mới" |
| "Không tự tạo hồ sơ âm tính" (mục 12.2) | **Bất biến:** không được sinh bản ghi/giá trị; chỉ ghi "chưa thấy trong hồ sơ hiện có" |
| "Không cần tự soạn thay khi chưa được hướng dẫn" | **Cấm có nút "tự động soạn tài liệu thay doanh nghiệp"** ở MVP |

**G2 — BẢNG KÊ TÀI LIỆU ở cuối tài liệu chính là output số 1 của hệ thống.** Bạn đã có sẵn format: `STT · Tên tệp · Mã mục liên quan · Ghi chú/ngày`. **MVP nên là: bỏ vào 50–100 file, máy tự điền bảng này và chỉ ra mã mục nào chưa có tài liệu nào.** Đó là một việc, rõ ràng, đo được, và tiết kiệm đúng phần việc tốn thời gian nhất.


**G3 — Mục A4 là mục tự động hóa tốt nhất toàn bộ danh sách.** Chứng nhận ATTP là lựa chọn "một trong các loại" theo danh sách Cục BVTV liệt kê (TT 38/2018 · TT 17/2018 · GMP · HACCP · ISO 22000 · IFS · BRC · FSSC 22000 · tương đương). Chỉ cần một bảng ~10 dòng là máy có thể: nhận diện loại chứng nhận → kiểm tra thuộc danh sách được chấp nhận → lấy ngày hết hạn → đối chiếu phạm vi với sản phẩm đăng ký. Ít rủi ro, giá trị cao, nên làm đầu tiên.


**G4 — Hai mẫu biểu mang mốc thời gian 2022 cần được xác nhận lại.** Mục **A2 dùng mẫu số 2-CV953/BVTV-ATTPMT ngày 13/4/2022** và mục **13 dùng "mẫu số 13" đính kèm**. Cả hai ra đời dưới thời Lệnh 248. Lệnh 280 đã có hiệu lực từ **1/6/2026**. → Cần hỏi ngay Phòng An toàn thực phẩm và Môi trường / Cục BVTV: **hai mẫu này còn được chấp nhận không, hay đã có mẫu mới?** Đây là việc nghiệp vụ, không phải việc IT, nhưng nếu không chốt thì hệ thống sẽ kiểm tra theo mẫu cũ.


**G5 — Dữ liệu cá nhân đã được chính tài liệu nhắc tới.** Mục 10.1 ghi rõ "không gửi thêm dữ liệu cá nhân không cần thiết nếu chưa được yêu cầu". → Hệ thống phải có chế độ **che/ẩn thông tin cá nhân** (tên, ngày sinh, kết quả khám) trước khi đưa vào AI và trước khi xuất file. Đây cũng là nghĩa vụ theo Luật Bảo vệ dữ liệu cá nhân 91/2025 (hiệu lực 1/1/2026).


## 4. Bảng chi tiết 40 mục

🔶 = cần dữ liệu tham chiếu · **T1** máy làm · **T2** máy tìm, người quyết · **T3** người làm · "Mã 148" = ánh xạ sang bản chi tiết

| Mã | Nội dung | Tầng | Mã 148 | Máy làm được gì | Lưu ý |
|---|:---|:-:|:--|---|---|
| **A1** | Giấy chứng nhận đăng ký doanh nghiệp và giấy đăng ký địa điểm kinh doanh/chi nhánh nếu có | **T1** | - | Tìm GPKD - trích tên/địa chỉ/mã số/người đại diện - kiểm tra địa chỉ nhà máy khớp chi nhánh | TÀI LIỆU MỎ NEO: dùng để so khớp chéo mọi tài liệu khác (A.1 / 1 / 13 / D.2) |
| **A2** | Bản cam kết theo mẫu số 2-CV953/BVTV-ATTPMT ngày 13/4/2022 | **T2** | 13.1-13.2 | Đối chiếu đúng mẫu - phát hiện vùng chữ ký và dấu (gợi ý) | ⚠ Mẫu ban hành 13/4/2022 - thời Lệnh 248. PHẢI xác nhận còn hiệu lực sau Lệnh 280 (1/6/2026) |
| **A3** | Sơ đồ quy trình sản xuất (từ tiếp nhận nguyên liệu đến thành phẩm) | **T2** | 6.1.1 | Tìm sơ đồ - đọc chú thích các bước | Tài liệu A3 và 6.1.1 có thể là MỘT bản dùng cho 2 mã |
| **A4** 🔶 | Một trong các giấy chứng nhận ATTP theo danh sách Cục BVTV liệt kê | **T1** | 1.2.2 | Nhận diện loại chứng nhận -> đối chiếu bảng được chấp nhận -> lấy ngày hết hạn -> đối chiếu phạm vi với sản phẩm đăng ký | 🔶 Cần bảng ~10 loại: TT 38/2018 - TT 17/2018 - GMP - HACCP - ISO 22000 - IFS - BRC - FSSC 22000 - tương đương. ĐÂY LÀ MỤC TỰ ĐỘNG HÓA TỐT NHẤT |
| **1** | Thông tin cơ bản doanh nghiệp và cơ sở theo biểu mẫu đăng ký (kèm công suất - sản lượng - xuất khẩu) | **T1** | 1.1.1-1.1.3 | Kiểm tra biểu mẫu đủ trường - trích số liệu công suất/sản lượng từ bảng | Phần trích số liệu từ bảng là T2 - hay sai nếu scan bảng kém |
| **2.1.1** | Sơ đồ tổng thể nhà máy ghi rõ tên các khu vực hoạt động | **T2** | 2.1.1 | Tìm sơ đồ - đọc tên khu vực |  |
| **2.1.2** | Ảnh khu vực nhà máy và môi trường xung quanh (thể hiện nguồn ô nhiễm nếu có) | **T2** | 2.1.2-2.1.4 | Phát hiện có ảnh - đọc chú thích nếu có | Máy KHÔNG đánh giá được nguồn ô nhiễm thực tế |
| **2.2** | Sơ đồ nhà xưởng thể hiện luồng người - nguyên liệu - nước - sản xuất và khu vực vệ sinh | **T2** | 2.2.1-2.2.4 | Tìm sơ đồ luồng - đọc chú thích | Gộp 4 mục của bản 148 thành 1 |
| **3.1** | Danh sách thiết bị và cơ sở vật chất chính kèm công suất thiết kế | **T2** | 3.1.1-3.1.2 | Trích bảng thiết bị - đối chiếu tổng công suất với mục 1 | Đối chiếu số công suất là phần tự động hóa được |
| **3.2** | Mô tả kiểm soát nhiệt độ và cách theo dõi nếu có kho lạnh | **T2** | 3.2.2-3.2.3 | Có điều kiện theo thuộc tính case | NHÁNH ĐIỀU KIỆN: chỉ yêu cầu nếu có kho lạnh |
| **4.1.1** | Ảnh nguồn nước tự cấp/công trình cấp nước phụ và biện pháp bảo vệ nếu có | **T2** | 4.1-4.2 | Có điều kiện - phát hiện ảnh và mô tả | NHÁNH ĐIỀU KIỆN |
| **4.1.2** | Kế hoạch và kết quả giám sát nước dùng trong sản xuất (đá/hơi nước nếu tiếp xúc trực tiếp) | **T2** | 4.4-4.5 | Tìm kế hoạch - trích điểm lấy mẫu/chỉ tiêu/phương pháp/tần suất - tìm phiếu kết quả |  |
| **4.1.3** 🔶 | Thông tin phụ gia lò hơi nếu hơi nước tiếp xúc trực tiếp với thực phẩm | **T2** | 4.6 | Cần danh mục phụ gia lò hơi được phép để đối chiếu | 🔶 NHÁNH ĐIỀU KIỆN |
| **5.1** | Quy trình/biện pháp tiếp nhận nguyên liệu và phụ gia + tiêu chuẩn kiểm tra + mẫu phiếu tiếp nhận | **T2** | 5.1.1-5.1.3 | Tìm SOP và mẫu phiếu đã điền (bản ghi) | Cả SOP và phiếu: cần tách riêng 'thiếu quy trình' / 'thiếu phiếu' |
| **5.2.1** 🔶 | Báo cáo kiểm nghiệm mới nhất về sự phù hợp với tiêu chuẩn ATTP Trung Quốc nếu áp dụng | **T2** | 5.2.4 | Tìm phiếu kết quả - trích mẫu/ngày/chỉ tiêu/đơn vị thử nghiệm | 🔶 Cần bảng chỉ tiêu theo mặt hàng. Tài liệu ghi rõ: chưa rõ chỉ tiêu thì hỏi Vexim/Cục TRƯỚC khi đặt xét nghiệm |
| **5.2.2** 🔶 | Tài liệu về vùng cung cấp nguyên liệu và điều kiện của nhà cung cấp | **T2** | 5.2.1-5.2.3 | Trích danh sách NCC/vùng - đối chiếu vùng với danh sách dịch hại cần giám sát | 🔶 'Yêu cầu dịch hại cụ thể cần xác nhận theo sản phẩm' |
| **5.3** 🔶 | Danh sách phụ gia thực phẩm sử dụng (tên - mục đích - lượng) | **T2** | 5.3.1-5.3.2 | Nếu có bảng phụ gia được phép: đối chiếu tên/công đoạn/lượng | 🔶 NHÁNH ĐIỀU KIỆN |
| **5.4.1** | Tài liệu chứng minh bao bì trong và ngoài phù hợp với sản phẩm | **T2** | 5.4.1 | Tìm tài liệu quy cách/giấy tờ bao bì |  |
| **5.4.2** | Mẫu nhãn của thành phẩm dự kiến xuất khẩu | **T1** | 5.4.2 | Phát hiện có mẫu nhãn/bản thiết kế/ảnh nhãn | Đối chiếu nội dung nhãn (T2) cần template theo sản phẩm |
| **6.1.1** | Sơ đồ quy trình sản xuất + các điểm kiểm soát tới hạn + biện pháp kiểm soát mối nguy | **T2** | 6.1.1-6.1.3 | Tìm sơ đồ và bảng CCP - đọc giới hạn/biện pháp | Có thể trùng tài liệu A3 |
| **6.1.2** | Nếu áp dụng HACCP: bảng phân tích mối nguy - kế hoạch - mẫu hồ sơ CCP - xử lý sai lệch - xác minh | **T2** | 6.1.2-6.1.6 | Tìm 5 thành phần - parse ngày cập nhật gần nhất | NHÁNH ĐIỀU KIỆN: 'nếu đang dùng hệ thống khác hoặc chưa rõ -> ghi chú để Vexim đối chiếu' |
| **6.2** 🔶 | Báo cáo kiểm nghiệm độc tố nấm mốc trong sản phẩm sản xuất - chế biến và lưu kho | **T2** | 6.2.1-6.2.3 | Tìm phiếu - trích analyte/kết quả/ngày - SO SÁNH với bảng giới hạn | 🔶 Phần đối chiếu giới hạn là T1 NẾU CÓ BẢNG GIỚI HẠN. Đây là mục có giá trị tự động hóa cao nhất nhóm B6 |
| **6.3** | Quy trình sản xuất - kế hoạch giám sát và kết quả giám sát phụ gia/chất tăng cường dinh dưỡng | **T2** | 6.3.1-6.3.2 | Có điều kiện | NHÁNH ĐIỀU KIỆN |
| **7.1** | Quy trình vệ sinh và khử trùng + phương pháp - tần suất - cách kiểm tra hiệu quả + nhật ký mẫu | **T2** | 7.1-7.4 | Tìm SOP VÀ nhật ký đã điền - parse ngày | Checklist đòi cả quy trình lẫn nhật ký mẫu: tách riêng 2 phần |
| **8.1** | Mô tả quản lý - sử dụng và bảo quản hóa chất | **T2** | 8.1.1-8.1.2 | Trích danh mục hóa chất và nơi lưu/cách cấp phát | Dùng làm tham chiếu đối chiếu với 7.1 |
| **8.2** | Quy trình ngăn ngừa dị vật và hồ sơ xử lý liên quan | **T2** | 8.2.1-8.2.3 | Tìm SOP và hồ sơ xử lý/kiểm tra thiết bị (sàng - nam châm - dò kim loại) |  |
| **8.3** | Phương pháp và sơ đồ bố trí kiểm soát côn trùng - chuột + hồ sơ năng lực nếu thuê ngoài | **T2** | 8.3.1-8.3.3 | Tìm sơ đồ bẫy + nhật ký - nếu thuê ngoài: tìm chứng nhận và kiểm tra hạn | NHÁNH ĐIỀU KIỆN: tự làm hay thuê ngoài |
| **8.4** | Quy trình quản lý chất thải và hồ sơ xử lý | **T2** | 8.4.1-8.4.3 | Tìm SOP và hồ sơ thu gom |  |
| **9.1** | Quy trình truy xuất và thu hồi + lấy một mã lô thành phẩm làm ví dụ truy ngược | **T3** | 9.1.1-9.1.4 | Quy trình: máy tìm được. Ví dụ mã lô: máy CHỈ gợi ý được một lô có đủ dữ liệu | MỤC KHÓ: phần 'ví dụ mã lô thật' không thể tự động tạo |
| **9.2** | Quy trình quản lý hàng nhập kho và xuất kho | **T2** | 9.2.1-9.2.2 | Tìm biểu mẫu/nhật ký nhập-xuất kho |  |
| **10.1** | Quy định khám sức khỏe trước làm việc và định kỳ + mẫu hồ sơ | **T2** | 10.1.1-10.1.2 | Đối chiếu danh sách nhân sự vs hồ sơ khám - chỉ lấy NGÀY không lấy chi tiết y tế | 🔒 DỮ LIỆU CÁ NHÂN: tài liệu ghi rõ 'không gửi thêm dữ liệu cá nhân không cần thiết' -> hệ thống phải che/ẩn khi gửi AI và khi xuất |
| **10.2** | Kế hoạch đào tạo hằng năm - nội dung - đánh giá và hồ sơ đào tạo nhân viên | **T2** | 10.2.1-10.2.2 | Parse năm kế hoạch - tìm danh sách tham dự và kết quả đánh giá |  |
| **10.3** | Hồ sơ đào tạo nhân sự quản lý về kiểm dịch và ATTP + sẵn sàng giải trình khi kiểm tra tại chỗ | **T3** | 10.3.1-10.3.3 | Phần hồ sơ: máy tìm. Phần 'sẵn sàng giải trình': KHÔNG LAM | PHẦN GIẢI TRÌNH LÀ CON NGƯỜI - tuyệt đối không giao máy |
| **11.1** 🔶 | Danh mục chỉ tiêu - phương pháp và tần suất kiểm tra thành phẩm | **T2** | 11.1-11.3 | Cần danh mục chỉ tiêu theo mặt hàng để đối chiếu | 🔶 |
| **11.2** | Nếu có phòng thử nghiệm nội bộ: hồ sơ năng lực. Nếu thuê ngoài: hồ sơ năng lực phòng được thuê | **T1** | 11.5-11.6 | Tìm chứng nhận - kiểm tra hạn - đối chiếu phạm vi với chỉ tiêu cần kiểm | NHÁNH ĐIỀU KIỆN: chỉ nộp MỘT nhánh. Máy kiểm tra hạn rất tốt |
| **12.1** 🔶 | Danh sách dịch hại kiểm dịch Trung Quốc quan tâm + kế hoạch/hồ sơ giám sát | **T2** | 12.1.1-12.1.3 | Cần danh sách dịch hại chuẩn theo sản phẩm rồi mới đối chiếu được | 🔶 QUAN TRỌNG NHẤT nhóm B12 - danh sách thay đổi theo protocol và phải cập nhật |
| **12.2** | Hồ sơ dịch hại phát hiện trong sản xuất/lưu kho + kết quả nhận diện (nếu thuê: hồ sơ đơn vị) | **T2** | 12.2.1-12.2.2 | Tìm hồ sơ phát hiện và báo cáo nhận diện - kiểm tra hạn chứng nhận đơn vị | ⚠ Tài liệu ghi rõ: 'không tự tạo hồ sơ âm tính' -> hệ thống PHẢI ghi 'chưa thấy trong hồ sơ hiện có' chứ không tự sinh bản ghi |
| **12.3** | Hồ sơ thực hiện biện pháp phòng trừ dịch hại ở khu vực sản xuất và lưu kho | **T2** | 12.3.1-12.3.3 | Parse 4-6 trường bắt buộc: ngày/vị trí/cách làm/hóa chất/người/KQ - đếm số lần - tính khoảng thời gian | Kiểm tra đủ trường là việc máy làm khá |
| **12.4** 🔶 | Phương pháp hun trùng và hồ sơ năng lực đơn vị/người thực hiện nếu cần hun trùng | **T3** | 12.4.1-12.4.4 | Cần yêu cầu cụ thể của TQ về hun trùng mới đối chiếu được | 🔶 NHÁNH ĐIỀU KIỆN + phần đối chiếu yêu cầu TQ là người |
| **13** | Bản tuyên bố theo mẫu số 13 đính kèm tài liệu Cục BVTV | **T2** | 13.1-13.4 | Đối chiếu đúng mẫu - phát hiện chữ ký và dấu (gợi ý) | ⚠ Dùng ĐÚNG mẫu Cục cung cấp - không tự soạn thay |

## 5. Năm bảng tham chiếu cần lập (ước lượng 200–400 dòng tất cả)

| # | Bảng | Phục vụ mục | Ai làm | Độ khó |
|---|---|---|---|---|
| 1 | Loại chứng nhận ATTP được chấp nhận (~10 dòng) | A4 | Chuyên viên Vexim | Dễ |
| 2 | Giới hạn/chỉ tiêu ATTP Trung Quốc theo mặt hàng (mycotoxin + chỉ tiêu thành phẩm) | 5.2.1 · 6.2 · 11.1 | Chuyên viên + đối chiếu tiêu chuẩn | Khó nhất |
| 3 | Danh sách dịch hại kiểm dịch TQ quan tâm theo mặt hàng | 12.1 · 5.2.2 | Chuyên viên + nguồn chính thức | Khó, phải cập nhật |
| 4 | Danh mục phụ gia/phụ gia lò hơi được phép | 5.3 · 4.1.3 · 6.3 | Chuyên viên | Trung bình |
| 5 | Mẫu biểu & template bắt buộc (mẫu 2-CV953, mẫu 13, template nhãn) | A2 · 13 · 5.4.2 | Nhận từ Cục BVTV | Dễ nhưng phải xác nhận |


Làm một lần, dùng cho mọi case. Đây là phần **không thể giao cho AI tự sinh** và là tài sản tích lũy thực sự của Vexim.


---

*Tổng 40 mục — T1: 5 (12%) · T2: 32 (80%) · T3: 3 (8%) · cần dữ liệu tham chiếu: 9 (22%)*