# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: Đặng Hồng Anh
Ngày: 15/9/2026

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT  |
| Thời gian gán `clip_02` (warm-up) | 10 phút |
| Thời gian gán `clip_01` | 30 phút |
| Số track đã vẽ trong `clip_01` | 8 |
| Số keyframe trung bình mỗi track | 10 |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

### Ca 1
- Clip / frame / ID: clip_01/ 87 / 8 và 4
- Tình huống: Xe bus và xe con đi vào khung hình nhưng thoạt đầu xe bus che khuất xe con và sau 1 vào frame thì xe con mới xuất hiện rõ
- Quyết định: tách biệt Id 2 xe , đến khi xe con ra khỏi phần giao như giữa 2 xe rõ ràng thì gán box cho phần nhìn thấy của xe  con.
- Lý do: để đảm bảo tính nhất quán của box

### Ca 2
- Clip / frame / ID: clip_01/ 185 / 3 và 6
- Tình huống: khi xe tải bắt đầu đi đến khuất xe con đang đỗ 
- Quyết định: vẫn tách biệt ID 2 xe, box của xe đỗ được giữ nguyên , box của xe tải ôm sát và đẩy đủ trong khung hình
- Lý do: xe đang đỗ thì box sẽ không đổi chỉ cần quan tâm đến xe tải đang đi đảm bảo khung hình của xe tải là được.

### Ca 3
- Clip / frame / ID: clip_02 / 39 / 4, 5, 6
- Tình huống: taxi đi đến khuất 2 xe bus đang đỗ
- Quyết định: đảm bảo khung hình taxi và giữ nguyên box 2 xe bus
- Lý do: xe đang đỗ box không đổi

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: `...`
- Lượt 2: `...`
- Lượt 3: `...`

Kiểm chéo với: `...`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `...`. Số lỗi bạn ấy tìm được trong bản của bạn: `...`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `...` |
| Thời điểm khóa | `...` |
| Số row / frame / track trước khi mở reference | `...` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | | | | | | | | | | |
| Sau rework | | | | | | | | | | |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có / chưa**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| | | | |
| | | | |
| | | | |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `...` |
| weights / hai tracker | `...` |
| conf / IoU / imgsz / classes | `...` |
| device | `...` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | | | | | | | | | | |
| ByteTrack control vs gold | | | | | | | | | | |
| BoT-SORT + ReID vs gold | | | | | | | | | | |
| ReID vs bạn | | | | | | | | | | |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

`...`

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

`...`

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

`...`

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

`...`

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`...`

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

`...`

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [ ] `outputs/eval_vs_gold.json`
- [ ] `outputs/model_bytetrack_clip_01.txt`
- [ ] `outputs/model_reid_clip_01.txt`
- [ ] `outputs/model_run_config.json`
- [ ] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
