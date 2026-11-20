# Thông tin học viên

- Họ và tên: Hoàng Quang Trường
- MSHV: 2A202603347
- Lớp (L2 / L3 / khác): E403 — AI.20K cohort 5 (L2-3)
- Tài khoản GitHub: truonghoangg25
- Repo bài nộp (URL): https://github.com/truonghoangg25/K5-L23-Day08-2A202603347-HoangQuangTruong-AI-Assisted-Labeling
- Commit mốc hoàn thiện nội dung: 20e612d (không bao gồm thay đổi metadata trong STUDENT.md).
- Máy: Windows + WSL2 Ubuntu; NVIDIA GeForce RTX 3050 Laptop GPU, 4 GB VRAM; PyTorch CUDA hoạt động.
- CVAT (Docker): v2.74.1; http://localhost:8080

## Kết quả tóm tắt

- Máy: accepted / edited / deleted: 452 / 0 / 2 (mẫu số: 454 box máy, theo `submission/qc/review_stats.md`); added: 0 (mẫu số: 452 đối tượng sau review).
- So với gold: precision / recall của máy: 67,8% / 90,9% (40 TP, 19 FP, 4 FN; 44 đối tượng Gold được chấm).
- Gold gốc: 47 annotations gồm 44 đối tượng thuộc các lớp và 3 `ignore_region`; vùng ignore không tham gia phép tính.
- Bắt bẫy trong lượt review mù ban đầu: 4/12 = 33,3%; dưới mục tiêu 80%.
- Thời gian: gán tay Gold khoảng 10 phút/4 ảnh = 2,5 phút/ảnh; duyệt batch khoảng 60 phút/24 ảnh = 2,5 phút/ảnh (ước lượng hồi tưởng).
- Lỗi QC mẫu: 0/5 ảnh có lỗi; 0/98 đối tượng có lỗi trong mẫu QC. Cận trên Wilson 95% của tỷ lệ ảnh lỗi là 43%.
- **Quyết định: HOLD** — lượt review mù chỉ bắt được 4/12 bẫy; mẫu QC nhỏ không đủ chứng minh tỷ lệ lỗi dưới 2%.
