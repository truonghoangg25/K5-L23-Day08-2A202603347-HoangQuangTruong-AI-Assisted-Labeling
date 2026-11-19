# Báo cáo Day 08 — 2A202603347 - Hoàng Quang Trường

> Báo cáo tổng hợp recipe, QC và ghi chú cá nhân. Số liệu kèm mẫu số và nguồn; giới hạn chưa đủ bằng chứng được nêu rõ.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | YOLO26-m | person 0.35; motorbike 0.50; các lớp còn lại giữ mặc định | r01/r02; threshold sweep trên 4 ảnh Gold. Bicycle có 3, bus 0, truck 1 đối tượng Gold nên chưa đủ dữ liệu chọn ngưỡng riêng. |
| three_wheeler | YOLOE-s | 0.15 | r03; threshold sweep trên 4 ảnh Gold: recall 100%, precision 78%, 7 đối tượng Gold. |

- Đối chiếu log YOLOE: `submission/model_log.csv` và `work/prelabel/t_yoloe/run.json` cùng ghi pilot `r03`, `conf=0.25`. `work/runs.csv` có dòng pilot trùng ID `r04` với lần chạy SAM và đường dẫn output khác; không dùng dòng đó làm nguồn xác định ID YOLOE.
- Recipe merge tái chạy gần nhất:
  `python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --iou 0.6 --conf 0.25 --split batch,gold --out work/prelabel/merged_review/pre.coco.json`
- Lần chạy lại tạo 647 box/28 ảnh; không khẳng định đây là file lịch sử đã import CVAT. File import lịch sử có 464 annotations/24 ảnh; `pre_traps.coco.json` có 531/28 ảnh.
- SAM: đã thử SAM 2.1 Tiny trên img_013 và SAM 2.1 Base ở r04. r04 tạo 16 masks/2 ảnh, 2.32 giây/ảnh. Tiny phù hợp hơn với giới hạn RTX 3050 Laptop 4 GB VRAM.
- Lỗi không sửa được bằng threshold: YOLO26 có thể nhầm xe ba bánh vào các lớp COCO. Cần kiểm tra ngữ nghĩa lớp; YOLOE chỉ là gợi ý bổ sung, vẫn phải review.
- Ví dụ box confidence cao sai / thấp đúng: chưa có bản ghi đủ chắc chắn để kết luận.
- Threshold được chọn trên chính 4 ảnh Gold dùng để đánh giá nên kết quả Gold có thể lạc quan.

## 2. AI error profile

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---:|---:|---:|
| accepted | 452 | 99.56% | 454 box máy |
| edited | 0 | 0% | 454 box máy |
| deleted | 2 | 0.44% | 454 box máy |
| added | 0 | 0% | 452 đối tượng sau review |


- Lớp có rủi ro sai ngữ nghĩa đáng chú ý: three_wheeler và các lớp phương tiện COCO gần giống. img_013 được ghi nhận trong calibration; chưa có thống kê lỗi theo lớp đủ chắc chắn để xếp hạng lớp tệ nhất.
- So với Gold: precision 67.8% (40 TP / 59 dự đoán được đối chiếu); recall 90.9% (40 TP / 44 đối tượng Gold). Gold có 47 annotations, gồm 44 đối tượng được chấm và 3 ignore_region. Nguồn: `submission/qc/review_stats.md`.

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Review mù ban đầu bắt được **4/12 bẫy = 33.3%**, thấp hơn mục tiêu 80%. Theo loại: missing 0/3; loose 0/3; wrong_class 1/3; extra 3/3. Các bẫy bị lọt: img_018, img_023, img_034, img_008, img_028, img_025, img_014, img_021.
- Bài học: không chỉ kiểm tra box đã có; phải quét toàn ảnh để tìm đối tượng máy bỏ sót.
- Accepted 99.56% không chứng minh review tốt vì chỉ tính trên box máy đề xuất, không đo đối tượng bị bỏ sót. Kết quả 12/12 sau khi hiệu chỉnh đáp án không phải điểm review mù; điểm ban đầu là 4/12.

## 4. Máy có đáng dùng không?

- Thời gian: Gold 10 phút/4 ảnh = 2.5 phút/ảnh; review batch khoảng 60 phút/24 ảnh = 2.5 phút/ảnh. Thời gian batch là ước lượng hồi tưởng, không phải đo bằng đồng hồ; hai công việc không hoàn toàn tương đương.
- Kết luận: chỉ dùng máy làm pre-label có người kiểm tra, chưa dùng để tự động phát hành. Gold precision 67.8%, recall 90.9%; blind trap chỉ bắt 4/12; QC mẫu nhỏ chưa chứng minh tỷ lệ lỗi dưới 2%.
- Ba quy tắc giảm thiên kiến:
  1. Quét toàn bộ ảnh trước khi xem box máy; chủ động tìm đối tượng không có pre-label.
  2. Kiểm tra riêng lớp xe ba bánh và các lớp COCO gần giống; không dùng confidence cao làm bằng chứng duy nhất.
  3. Giữ kín đáp án bẫy đến khi kết thúc review; báo cáo riêng điểm blind trước và sau khi mở đáp án.


## 5. Mask SAM cho 3 vật (Part 5)

Do giới hạn thời gian, áp dụng phương án rút gọn trong GUIDE: thử nghiệm trên hai ứng viên vật thể và bổ sung ảnh đối chứng bằng phiên bản SAM khác. Các kết quả dưới đây là mask đầu ra của click prompt; chưa xác nhận trực quan rằng mask bao trọn vật thể hoặc tách đúng người lái/hàng hóa.

| Ảnh | SAM (phiên bản) | Số click | Diện tích mask | Nhận xét / giới hạn | File trong `masks/` |
|---|---|---:|---:|---|---|
| img_013 | SAM 2.1 Tiny | 1 positive | 2,951 px (~0.4%) | Mask rất nhỏ; chưa xác nhận ranh giới vật thể | `img013_one_positive.jpg` |
| img_013 | SAM 2.1 Tiny | 2 positive | 269,818 px (~38.6%) | Diện tích tăng mạnh khi thêm positive click | `img013_two_positive.jpg` |
| img_013 | SAM 2.1 Tiny | 2 positive + 1 negative | 275,126 px (~39.4%) | Negative click không làm diện tích mask giảm trong lần thử này | `img013_positive_negative.jpg` |
| img_003 | SAM 2.1 Base | 1 positive | 5,789 px (~0.8%) | Mask nhỏ; chưa thể kết luận đã bao trọn xe hoặc phần bị che | `occluded_img003.jpg` |
| img_008 | SAM 2.1 Base | 1 positive | 11,718 px (~1.7%) | Mask nhỏ; chưa thể kết luận đã bao trọn xe/hàng hóa | `cargo_tricycle_img008.jpg` |
| img_013 | SAM 2.1 Tiny | 2 positive + 1 negative | 275,126 px (~39.4%) | Ảnh đối chứng phiên bản; cùng cấu hình click với thử nghiệm Tiny trước đó | `tuktuk_img013_tiny.jpg` |
| img_013 | SAM 2.1 Base | 2 positive + 1 negative | Chưa ghi nhận | Kết quả click prompt từ notebook Cell 19; chưa xác nhận trực quan ranh giới mask | `tuktuk_img013.jpg` |

- Tổng cộng có 7 ảnh mask trên 3 ảnh nguồn. Đây là các cấu hình thử nghiệm, không phải bằng chứng đã hoàn thành mask chuẩn cho ba loại vật thể độc lập.
- Chưa xác nhận chắc chắn mask có bao gồm người lái hoặc hàng hóa; không suy diễn loại xe cụ thể chỉ từ tên lớp `three_wheeler`.
- Đối chiếu guideline mục 4.2: cần kiểm tra mask sau mỗi click; không mặc định negative click luôn thu hẹp mask. Ranh giới kính/cửa sổ vẫn chưa chốt, ghi nhận tại CAL-03.
- Interactor SAM trong CVAT: chưa có kết quả kiểm thử đủ chắc chắn để kết luận hoạt động hay không.

## 6. Calibration (Part 6)

- Số quyết định: **3**, trong `submission/qc/calibration_decisions.csv`. Quan trọng nhất: CAL-02 tại img_018 vì bẫy bỏ sót person cho thấy phải tìm cả đối tượng không có pre-label.
- Quy tắc mới ở `guideline_v1.1_calibrated.md`: kiểm tra sai lớp xe ba bánh (CAL-01); quét toàn ảnh tìm đối tượng bị bỏ sót (CAL-02); không tự suy diễn ranh giới mask chưa được quy định (CAL-03).
- Nguyên nhân gốc: kết hợp giới hạn mô hình và quy trình review chưa kiểm tra đủ vùng không có pre-label; một số quy tắc trong guideline vẫn là câu hỏi mở.


## 7. QC và quyết định (Part 6)

- Mẫu: **5 ảnh** (0 ảnh can_review + 5 ảnh ngẫu nhiên, seed 42); người kiểm là chính người thực hiện review. Gồm img_003, img_006, img_014, img_031, img_035; tổng 98 đối tượng.
- Lỗi theo đối tượng: **0/98 = 0%**; theo ảnh: **0/5 = 0%**. Ngưỡng Release yêu cầu tỷ lệ lỗi không quá 2%.
- Top lỗi trong mẫu QC: không ghi nhận lỗi ở 5 ảnh đã kiểm tra. Kết quả này không chứng minh toàn bộ batch không có lỗi.
- Giới hạn mẫu nhỏ: cận trên Wilson 95% của tỷ lệ ảnh lỗi là **43%** theo `qc_report_auto.md`; 5 ảnh chưa đủ chứng minh tỷ lệ lỗi thực tế dưới 2%.
- Script kiểm được các lỗi thể hiện trong dữ liệu QC và thống kê đã ghi. Script không thay thế rà soát trực quan toàn ảnh, không đảm bảo phát hiện đối tượng bị bỏ sót và không loại bỏ thiên lệch khi người review biết dữ liệu.
- **Quyết định: HOLD**, ngày 2026-11-20. Lý do: blind review chỉ bắt 4/12 bẫy (33.3%), thấp hơn mục tiêu 80%; mẫu QC 5 ảnh quá nhỏ để chứng minh tỷ lệ lỗi dưới 2%.
- Việc cần làm tiếp: cải thiện checklist tìm đối tượng bị bỏ sót; thực hiện blind trap test mới với đáp án được giữ kín; tăng cỡ mẫu QC và review độc lập trước khi cân nhắc Release.

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài:** có dùng ChatGPT hỗ trợ phân tích số liệu, giải thích lỗi, đề xuất lệnh và rà soát báo cáo. Số liệu được đối chiếu với file trong repository và output terminal.
- Gold có bị nhiễm không: **cần người thực hiện xác nhận** liệu có xem pre-label hoặc hỏi AI trước khi hoàn thành gán Gold hay không.
- Máy / GPU / CVAT: Ubuntu trên WSL2; NVIDIA GeForce RTX 3050 Laptop GPU 4 GB VRAM; CVAT v2.74.1 tại `http://localhost:8080`. Đã thử YOLO26, YOLOE và SAM 2.1.
- Dùng cache `data/cache/` thay vì tự chạy: chưa xác nhận.
- Lỗi / bug script: phải kiểm tra lại kết quả merge và phân biệt file merge tái chạy với file pre-label lịch sử đã import; cần ghi rõ nguồn và phiên bản artifact.
- Một điều làm khác lần sau: review blind theo checklist toàn ảnh trước khi xem pre-label; giữ kín đáp án bẫy đến khi kết thúc; tăng mẫu QC và ghi thời gian thực tế thay vì ước lượng hồi tưởng.
