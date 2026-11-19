# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603347 - Hoàng Quang Trường

> Tài liệu này chỉ bổ sung các quyết định và quy tắc mới so với guideline v1.0. Các câu hỏi chưa đủ bằng chứng được giữ ở trạng thái chưa chốt.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | case_id |
|---|---|---|---|---|
| 1 | Xe đạp điện thuộc bicycle hay motorbike? | Chưa chốt. Cần ví dụ rõ hơn và thống nhất cách phân biệt trước khi đưa ra quy tắc chung. | Chưa có ảnh đủ căn cứ | — |
| 2 | Nhóm xe máy dày đặc: tách từng xe hay dùng ignore_region? | Chưa chốt; cần đối chiếu yêu cầu annotation và thống nhất giữa người gán nhãn. | Chưa có ảnh đủ căn cứ | — |
| 3 | Hàng hóa nhô ra khỏi phương tiện trên 20%? | Chưa chốt; cần ví dụ biên cụ thể. | Chưa có ảnh đủ căn cứ | — |
| 4 | Hành khách mờ trong xe ba bánh có mái che? | Chưa chốt; cần ví dụ đủ rõ để thống nhất tiêu chí. | Chưa có ảnh đủ căn cứ | — |
| 5 | Ranh giới mask ở kính chắn gió và cửa sổ? | Chưa chốt; không tự suy diễn khi chưa có quy tắc rõ và đồng thuận reviewer. | img_013 | CAL-03 |
| 6 | Box chồng lấn trong cảnh ban đêm? | Chưa chốt; cần thêm ví dụ và hướng dẫn cụ thể. | Chưa có ảnh đủ căn cứ | — |
| 7 | Confidence tối thiểu để máy được tự động chấp nhận? | Không đặt ngưỡng auto-accept chung từ kết quả hiện tại; vẫn cần người review. | — | — |

## 2. Ca khó mới (từ lỗi của máy và bẫy lọt)

| Ảnh | Tình huống | Máy làm gì | Quyết định | Quy tắc mới |
|---|---|---|---|---|
| img_013 | Xe ba bánh bị nhầm vào lớp COCO | YOLO26 có thể gán xe ba bánh vào car, truck, motorbike hoặc bicycle | Không tự động chấp nhận lớp COCO; dùng YOLOE làm nguồn gợi ý bổ sung và kiểm tra thủ công | Không dùng confidence threshold để sửa lỗi sai ngữ nghĩa. Box YOLOE cũng phải được người kiểm tra. (CAL-01) |
| img_018 | Bỏ sót person trong lượt review mù | Không có box đề xuất cho đối tượng bị thiếu | Quét toàn bộ ảnh, chủ động tìm đối tượng ở vùng không có pre-label | Checklist review phải có bước tìm đối tượng bị bỏ sót, không chỉ kiểm tra box có sẵn. (CAL-02) |
| img_013 | Ranh giới mask ở kính và cửa sổ chưa rõ | SAM tạo mask nhưng kết quả thử nghiệm chưa đủ để chốt quy tắc ranh giới | Giữ trạng thái chưa chốt; cần thêm ví dụ và thống nhất reviewer | Không tự suy diễn ranh giới mask khi guideline chưa quy định rõ. (CAL-03) |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Bổ sung quy tắc kiểm tra sai lớp xe ba bánh, tìm đối tượng bị bỏ sót và ghi nhận ranh giới mask chưa thống nhất; giữ rõ các câu hỏi chưa chốt | CAL-01, CAL-02, CAL-03 |
