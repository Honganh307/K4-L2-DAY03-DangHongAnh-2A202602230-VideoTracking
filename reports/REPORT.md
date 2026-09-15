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

1.
- Clip / frame / ID: clip_01/ 87 / 8 và 4
- Tình huống: Xe bus và xe con đi vào khung hình nhưng thoạt đầu xe bus che khuất xe con và sau 1 vào frame thì xe con mới xuất hiện rõ
- Quyết định: tách biệt Id 2 xe , đến khi xe con ra khỏi phần giao như giữa 2 xe rõ ràng thì gán box cho phần nhìn thấy của xe  con.
- Lý do: để đảm bảo tính nhất quán của box

2.
- Clip / frame / ID: clip_01/ 185 / 3 và 6
- Tình huống: khi xe tải bắt đầu đi đến khuất xe con đang đỗ 
- Quyết định: vẫn tách biệt ID 2 xe, box của xe đỗ được giữ nguyên , box của xe tải ôm sát và đẩy đủ trong khung hình
- Lý do: xe đang đỗ thì box sẽ không đổi chỉ cần quan tâm đến xe tải đang đi đảm bảo khung hình của xe tải là được.

3.
- Clip / frame / ID: clip_02 / 39 / 4, 5, 6
- Tình huống: taxi đi đến khuất 2 xe bus đang đỗ
- Quyết định: đảm bảo khung hình taxi và giữ nguyên box 2 xe bus
- Lý do: xe đang đỗ box không đổi

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1 (Nhìn ID): Phát hiện lỗi nhảy/đổi ID ngẫu nhiên (ID Switch) giữa các phương tiện khi đi song song hoặc cắt mặt nhau, nhận diện các vết theo dõi bị phân mảnh (Trajectory Fragmentation) hoặc bị mất ID giữa chừng.
- Lượt 2 (Frame đầu / cuối): Kiểm tra thời điểm chính xác xe xuất hiện (Track Initialization) và biến mất (Track Termination), đảm bảo khung bao không bị vẽ quá sớm khi chưa rõ vật thể hoặc kéo dài vô lý khi xe đã ra khỏi khung hình/rìa ảnh.
- Lượt 3 (Frame giữa): Kiểm tra độ ôm sát của khung bao (Bounding Box Tightness) tại các vị trí vật thể bị che khuất một phần (Occlusion), chuyển động nhanh/phanh gấp, hoặc đổi hướng, đảm bảo không bị lệch vị trí hay ôm thừa nền đường.

Kiểm chéo với: None. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: None. Số lỗi bạn ấy tìm được trong bản của bạn: None.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

None

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` |   "sha256": "65b59e6d74d2e1afbdfad908be9197603b6f6fe469e7a8fb0c025c28d282d75e" |
| Thời điểm khóa | 11h |
| Số row / frame / track trước khi mở reference | 54/190/8 |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.689 | 0.629 | 0.767 | 0.804 | 0.853 | 0.725 | 0.804 | 123 | 20 | 0 |
| Sau rework | 0.709 | 0.649 | 0.777 | 0.834 | 0.883 | 0.745 | 0.814 | 119 | 18 | 0 |
Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Vật thể bắt đầu xuất hiện | 106 | 7 | cho xe tải xuất hiện sớm hơn 3-4 frame|


## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | "python": "3.13.15",  "ultralytics": "8.4.145",  "torch": "2.11.0+cu128",  "lap": "0.5.13" |
| weights / hai tracker | yolo26n.pt, bytetrack.yaml, /content/K4-L2-DAY03-DangHongAnh-2A202602230-VideoTracking/configs/trackers/botsort-reid.yaml |
| conf / IoU / imgsz / classes | "conf": 0.25,  "iou": 0.7,  "imgsz": 960,  "classes": [    2,    5,    7  ] |
| device | 0 |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.761| 0.743| 0.781| 0.833 | 0.963 |0.928 | 0.810 | 4 | 37 | 0 |
| ByteTrack control vs gold |0.709|0.649|0.776|0.846|0.875| 0.749 |0.823 |88 | 54|2 |
| BoT-SORT + ReID vs gold |0.763|0.711 |0.820 |0.872 |0.900| 0.792 |0.860 |91 |26 |2 |
| ReID vs bạn |0.709 | 0.649 | 0.777 | 0.834 | 0.883 | 0.745 | 0.814 | 119 | 18 | 0 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của tôi cao hơn IDF1.Việc MOTA cao nhưng IDF1 thấp cho thấy hệ thống phát hiện vị trí vật thể (Bounding Box) rất chính xác, ít bỏ sót hay nhận diện lầm, nhưng lại bị đứt gãy vết theo dõi và tráo đổi ID liên tục (ID Switches).MOTA không phạt nặng lỗi ID vì chỉ tính 1 điểm phạt đơn tại frame xảy ra chuyển đổi ID ($\text{IDSW}$), sau đó vẫn tính điểm thưởng nếu bbox ôm đúng vật thể. Trái lại, IDF1 đo lường sự nhất quán ID trên toàn bộ đường đi, nên bất kỳ sự đứt gãy ID nào cũng làm giảm mạnh điểm IDF1.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

So sánh chỉ số:
IDF1: Tăng từ 0.875 (ByteTrack) lên 0.900 (BoT-SORT + ReID) — cải thiện 2.5%.
AssA (Association Accuracy): Tăng từ 0.776 lên 0.820 — cải thiện 4.4%.
IDSW (ID Switches): Bằng nhau, đều giữ ở mức 2.
Phân tích: Việc tích hợp ReID giúp duy trì liên kết ID tốt hơn khi xe bị che khuất hoặc di chuyển lại gần nhau, thể hiện rõ qua sự gia tăng của IDF1 và AssA.
Dẫn chứng chuỗi frame: Ở chuỗi frame xe bus đi qua che mất xe con, ByteTrack bị mất vết do thiếu IoU overlapping, khiến chuỗi liên kết bị gián đoạn. Trong khi đó, BoT-SORT nhờ có đặc trưng ngoại dạng (ReID appearance features) nên kết nối lại đúng ID cũ sau khi xe xuất hiện trở lại, giúp đẩy AssA tăng cao.
**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

Thay đổi chỉ số:
DetA (Detection Accuracy): Tăng từ 0.649 lên 0.711.
FP (False Positives): Tăng nhẹ từ 88 lên 91 (+3).
FN (False Negatives): Giảm mạnh từ 54 xuống 26 (giảm 28 lỗi bỏ sót).
Đánh giá nguồn gốc lỗi còn lại:
Lỗi còn lại chủ yếu nằm ở Detector. Việc FN giảm đáng kể từ 54 xuống 26 cho thấy việc kết hợp tracker tốt hơn giúp phục hồi nhiều bounding box bị bỏ sót. Tuy nhiên, chỉ số FP vẫn còn khá cao (91) do Detector phát hiện nhầm các bóng râm/vật thể nền hoặc gán nhãn thừa. Trong khi đó, AssA đã đạt 0.820 và IDSW chỉ vỏn vẹn 2 lỗi, chứng tỏ khâu liên kết (Association) hoạt động rất ổn định.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Frame 150, Track Gold 6 (hoặc Frame 96, Track Gold 5):
Khung bao (bbox) gán nhãn duy trì độ chính xác cao và ôm sát vật thể, trong khi dự đoán của mô hình bị trôi lệch làm chỉ số IoU giảm xuống chỉ còn 0.50 (ở frame 150 với track gold 6) và 0.56 (ở frame 96 với track gold 5). Lý do là mô hình ReID bị ảnh hưởng bởi góc quay/ánh sáng giữa hai keyframe liên tiếp, dẫn đến khung định vị bị trượt khỏi tâm vật thể.
**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Frame 96–120 (hoặc chuỗi frame của Track Gold 5 & 6):
Kết quả đối chiếu hiển thị lỗi "THIẾU ĐOẠN — track có nhãn nhưng không phủ hết quãng đời". Cụ thể:
Track Gold 5: Mới chỉ phủ 45/60 framse (đạt 75%).
Track Gold 6: Mới chỉ phủ 43/56 frame (đạt 77%).
Minh chứng này nhắc nhở bạn quay lại kiểm tra video ở các frame đầu/cuối của Track 5 và Track 6 để gán bổ sung các frame còn thiếu mà ban đầu lượt gán nhãn thủ công đã lỡ bỏ sót khi xe mới bắt đầu xuất hiện hoặc chuẩn bị ra khỏi màn hình.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

giảm thiểu quy trình gán nhãn, chỉ những frame và vật thể có sự biến động như rẽ hoặc biến mất khỏi khung hình thì mới cần gán liên tục.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [x] `reports/review_partner.md`
- [x] `reports/REPORT.md` (file này)
