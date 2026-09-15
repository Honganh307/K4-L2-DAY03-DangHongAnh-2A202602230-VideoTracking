# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Đặng Hồng Anh`
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `None`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | Tránh hiện tượng phân mảnh quỹ đạo, giúp thấy được quỹ đạo liên tục của vật thể khi bị che khuất tạm thời.  |
| Xe bị che lâu hơn ngưỡng trên | Tạo Track ID mới khi xe xuất hiện lại. | Tránh lỗi gán nhãn sai ID (ID Switch) do mất đặc trưng bối cảnh quá lâu, khiến mô hình tracking dễ bị nhầm lẫn với vật thể khác. |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | Khi đã ra khỏi tầm nhìn, phương tiện không còn liên kết  liên tục; việc tạo ID mới giúp thuật toán đánh giá (như MOTA/IDF1) đo lường chính xác. |
| Hai xe cắt nhau / chồng lên nhau | Giữ nguyên ID riêng cho từng xe | Đảm bảo tính nhất quán Bounding Box. |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: độ tin cậy nhận diện rõ nét bằng mắt thường. |
| Xe đang đỗ, không di chuyển | Vẫn gán nhãn và giữ nguyên ID xuyên suốt các frame; duy trì khung bao cố định vị trí trừ khi có sự thay đổi góc quay của camera. |
| Keyframe đặt dày ở đâu | Đặt dày tại các frame có sự thay đổi chuyển động đột ngột (xe rẽ nhánh, phanh gấp, đổi làn) hoặc thời điểm xe bắt đầu/kết thúc bị che khuất (occlusion events). |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

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

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- Luật hai xe cắt nhau / chồng lên nhau cần bổ sung như sau: xe bị che chỉ vẽ phần nhìn thấy, xe phía trước giữ nguyên khung bao đầy đủ.
