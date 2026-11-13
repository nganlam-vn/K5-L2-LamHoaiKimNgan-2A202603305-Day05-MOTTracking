# Báo cáo cá nhân — Lâm Hoài Kim Ngân - 2A202603305

Đoạn video: MOT17-09, 150 frame (frame 1–150 / frame 0–149 trên CVAT), nguồn MOTChallenge, giấy phép CC BY-NC-SA 3.0.

## 0. Thông tin học viên

- Họ tên: Lâm Hoài Kim Ngân
- MSSV: 2A202603305
- Mã đề (A/B/C): A
- Email: lamhoaikimngan91@gmail.com
- Link repository: https://github.com/nganlam-vn/K5-L2-LamHoaiKimNgan-2A202603305-Day05-MOTTracking
- Giờ bắt đầu: 13:30
- Giờ kết thúc: 17:30

## 1. Xem trước đoạn video (CP0)

Ghi **trước khi vẽ**, mỗi người một dòng.

| Mã | Frame xuất hiện | Frame cuối thấy được | Rẽ / dừng / đổi tốc độ ở frame | Bị che ở frame | Ghi chú |
|---|---|---|---|---|---|
| P1 | 0 | 149 | 20–30 (chậm lại), 50 (bước ra), 100–110 (đổi hướng nhẹ sang phải) | 30–59 (che một phần ở 30–42 và 50–59, khuất hẳn ở 43–49) | Người áo len đen đi qua shop Vodafone từ trái qua phải, bị người áo khoác đen (Person 2) che khuất |
| P2 | 61 | 149 | Di chuyển đều, không đổi hướng đột ngột | 61–70 (bị khung kính / mép cửa shop che một phần khi mới xuất hiện) | Xuất hiện từ frame 61 bên trong cửa hàng Vodafone, đi cùng hướng với P1 |
| P3 | 0 | 149 | 60–80 (bước đều, tịnh tiến dần lại gần camera) | Không bị che (0 frame occluded/outside) | Đi bộ dọc theo lề đường bên phải, kích thước box tăng dần khi lại gần camera |
| P4 | 39 | 149 | 49–60 (di chuyển dọc lề phải sát mép khung hình) | 39, 49, 59 (che một phần), 50–58 (khuất mép / ngoài khung hình) | Xuất hiện từ frame 39 ở mép phải màn hình, đi cùng phía với P3 |
| P5 | 0 | 148 | 100–110 (chậm lại do người khác cắt ngang) | 110–119 (che một phần), 120–139 (khuất hẳn sau người khác) | Người đi bộ ở xa giữa quảng trường, kích thước box nhỏ, bị che khuất đoạn dài cuối video |

## 2. Keyframe và thời gian (CP1, CP4)

Số keyframe lấy từ kết quả `src/check_submission.py`.

| Mã | Số keyframe | Keyframe ở frame | Số frame có mặt | Keyframe / 100 frame | Thời gian (phút) | Chỗ khó |
|---|---|---|---|---|---|---|
| P1 | 15 | 0, 10, 20, 30, 50, 60, 70, 80, 90, 100, 110, 120, 130, 140, 149 | 143 | 10.5 | 20 | Xử lý che khuất amodal khi bị người khác che (frame 30–59), căn mép chân qua kính phản chiếu |
| P2 | 10 | 61, 71, 81, 91, 101, 111, 121, 131, 141, 149 | 89 | 11.2 | 10 | Xuất hiện muộn ở frame 61, bị che một phần thân bởi khung cửa kính |
| P3 | 16 | 0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110, 120, 130, 140, 149 | 150 | 10.7 | 15 | Đi lại gần camera khiến kích thước box tăng nhanh, cần chỉnh scale đều đặn để tránh trôi viền |
| P4 | 21 | 39, 40, 49, 50, 59, 60, 69, 70, 79, 89, 90, 99, 100, 109, 110, 119, 120, 129, 130, 139, 140, 149 | 102 | 20.6 | 18 | Xuất hiện sát mép khung hình, sải bước không đều, cần nhiều keyframe để giữ box khít người |
| P5 | 11 | 0, 10, 20, 30, 40, 50, 80, 100, 104, 110, 140 | 129 | 8.5 | 12 | Kích thước box nhỏ do ở xa, đoạn bị che khuất khá dài ở frame 120–139 |
| **Tổng** | **73** | — | **613** | **11.9** | **75** | — |

Keyframe / 100 frame của cả bài = tổng keyframe ÷ tổng số frame có mặt × 100 = 73 ÷ 613 × 100 = **11.9** keyframe / 100 frame.

Bạn đặt keyframe nhiều nhất ở người nào? Vì sao (người đó rẽ, dừng, lại gần camera hay bị che)?

- Tôi đặt nhiều keyframe nhất ở người **P4** (21 keyframe, mật độ 20.6 keyframe / 100 frame) và **P3** (16 keyframe).
- **Lý do:** Đối với P4, người này xuất hiện ở mép ngoài cùng bên phải khung hình từ frame 39, di chuyển lại gần camera với tốc độ bước chân không đều và có đoạn bị che khuất/khuất mép (frame 50). Do chuyển động bước đi và sự thay đổi phối cảnh góc rộng ở mép camera diễn ra nhanh, phép nội suy tuyến tính (Linear Interpolation) dễ bị trễ hoặc lệch IoU nếu khoảng cách keyframe quá thưa. Vì vậy, tôi phải cắm thêm keyframe ở các mốc ngắn (như 39, 40, 49, 50, 59, 60...) để đảm bảo bounding box bám khít sát toàn thân. Với P1, tôi cũng cần 15 keyframe để kiểm soát chặt chẽ các mốc trước, trong và sau đoạn bị che khuất qua cửa hàng Vodafone.

## 3. Đoạn bị che (CP2)

Người bị che: `P1`

| Mốc | Frame |
|---|---:|
| Bắt đầu bị che một phần (bật `occluded`) | 30 |
| Mất hẳn (bật `outside`) | 43 |
| Hiện lại (tắt `outside`) | 50 |
| Thấy rõ trở lại (tắt `occluded`) | 60 |

- Thời gian khuất hẳn: **7** frame = **0.23** giây (7 ÷ 30 fps ≈ 0.23s).
- Quyết định: **giữ cùng ID** (Track ID 3 trong MOT 1.1 / Track 3 trong CVAT).
- Lý do (trả lời 3 câu: khuất bao lâu so với 60 frame, ngoại hình có khớp, chỗ hiện lại có đúng hướng và tốc độ):
  1. *Khuất bao lâu so với ngưỡng 60 frame:* Thời gian khuất hẳn chỉ diễn ra trong 7 frame (~0.23 giây), ngắn hơn rất nhiều so với ngưỡng quy ước 60 frame (2 giây) của bài lab.
  2. *Ngoại hình có khớp không:* Người tái xuất hiện mặc áo len đen dài tay, quần tối màu, mang cùng vóc dáng, chiều cao và đặc điểm hình thể hoàn toàn trùng khớp với người trước khi bị che.
  3. *Chỗ hiện lại có đúng hướng và tốc độ không:* Vị trí xuất hiện lại tại frame 50 nằm ngay phía bên phải của người che khuất (Person 2), tiếp nối chính xác quỹ đạo chuyển động tịnh tiến từ trái sang phải với vận tốc bước đi đều đặn qua mặt tiền cửa hàng Vodafone.

Ảnh chụp màn hình:

![Trước khi che](images/che_1_truoc.png)
![Giữa đoạn che, thấy cờ trên panel object](images/che_2_giua.png)
![Sau khi hiện lại, cùng ID](images/che_3_sau.png)

## 4. Tự review ID (CP3)

Lượt 1 theo người (đi ngược từ frame 149 về 0 trên CVAT), lượt 2 theo cảnh (nhảy 10 frame). Ít nhất 3 dòng; không có lỗi thì ghi một dòng "đã kiểm hai lượt, không lỗi".

Loại lỗi dùng đúng một trong 7 mã: `id_switch`, `fragmentation`, `lost`, `merge`, `false_track`, `drift`, `flag`.

| Frame | Mã người | Loại lỗi | Mô tả một câu | Phát hiện ở lượt | Đã sửa? |
|---:|---|---|---|---|---|
| 30–42 | P1 | `flag` | Quên bật thuộc tính `occluded` khi P1 bắt đầu bị thân người P2 che khuất một phần | Lượt 1 (theo người) | Đã sửa (bật cờ `occluded` từ frame 30 đến 42) |
| 43–49 | P1 | `flag` | Vẫn để box ước lượng hiển thị khi P1 đã bị che lấp 100% thay vì bật `outside` | Lượt 1 (theo người) | Đã sửa (bật cờ `outside` từ frame 43 đến 49) |
| 75 | P3 | `drift` | Bounding box bị trôi tụt lại phía sau lưng người do người đi nhanh dần lại gần camera | Lượt 1 (theo người) | Đã sửa (thêm keyframe căn chỉnh lại mép hộp) |
| 50 | P4 | `lost` | Quên tắt cờ `outside` khiến đối tượng bị mất box hiển thị dù người đã bước lộ diện | Lượt 2 (theo cảnh) | Đã sửa (tắt `outside` tại frame 50 và chỉnh lại box) |

Tổng hợp số lỗi theo loại:

| `id_switch` | `fragmentation` | `lost` | `merge` | `false_track` | `drift` | `flag` |
|---:|---:|---:|---:|---:|---:|---:|
| 0 | 0 | 1 | 0 | 0 | 1 | 2 |

Lỗi nào chỉ lượt 2 (theo cảnh) mới thấy, lượt 1 (theo người) không thấy? Vì sao?

- **Lỗi chỉ lượt 2 mới thấy:** Lỗi `lost` của đối tượng P4 tại frame 50 và sự tương quan giữa các đối tượng trong cùng một không gian.
- **Giải thích:** Khi thực hiện lượt 1 (soát theo từng người), mắt người gán nhãn tập trung bám sát quỹ đạo cục bộ của một đối tượng cụ thể nên dễ bỏ quên bức tranh toàn cảnh. Đến lượt 2 (soát theo cảnh toàn cục, nhảy mỗi bước 10 frame), ta quan sát đồng thời cả 5 bounding box trên cùng một màn hình, do đó lập tức nhận ra sự bất thường khi số lượng bounding box giảm đột ngột từ 5 xuống 4 ở frame 50, phát hiện ngay đối tượng bị mất box (`lost`) mà lượt 1 dễ bỏ sót.

## 5. File xuất và kết quả tự kiểm (CP4)

| Kiểm tra | Kết quả |
|---|---|
| Tên file MOT 1.1 | `LamHoaiKimNgan_mot.zip` |
| Tên file CVAT for video 1.1 | `LamHoaiKimNgan_cvat_video.zip` |
| Số track ID khác nhau trong `gt/gt.txt` (phải là 5) | 5 track ID |
| Frame `outside` không có dòng trong `gt.txt` (đúng / sai) | Đúng |
| Job đã completed trên CVAT máy cá nhân | Đã completed |

Dán phần tóm tắt in ra từ `src/check_submission.py`:

```text
Số frame in theo CVAT (bắt đầu từ 0). Trong gt.txt, frame_id = frame CVAT + 1.

=== File MOT 1.1: LamHoaiKimNgan_mot.zip ===
  Số track ID: 5   |   Tổng số box: 613
  [OK]        Đúng 5 track ID.

  Track | Frame có mặt | Số frame | Đoạn vắng (outside)
      1 |   0–148      |      129 | 120–139
      2 |   0–149      |      150 | -
      3 |   0–149      |      143 | 43–49
      4 |  39–149      |      102 | 50–58
      5 |  61–149      |       89 | -

=== File CVAT for video 1.1: LamHoaiKimNgan_cvat_video.zip ===

  Track | Nhãn   | Frame thấy | Keyframe | KF/100 frame | Frame occluded | Lần bật outside
      0 | person |        129 |       11 |          8.5 |             10 |               2
              Track 0: keyframe ở frame [0, 10, 20, 30, 40, 50, 80, 100, 104, 110, 140]
      1 | person |        150 |       16 |         10.7 |              0 |               0
              Track 1: keyframe ở frame [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110, 120, 130, 140, 149]
      2 | person |        102 |       21 |         20.6 |              3 |               1
              Track 2: keyframe ở frame [39, 40, 49, 59, 60, 69, 70, 79, 89, 90, 99, 100, 109, 110, 119, 120, 129, 130, 139, 140, 149]
      3 | person |        143 |       15 |         10.5 |             23 |               1
              Track 3: keyframe ở frame [0, 10, 20, 30, 50, 60, 70, 80, 90, 100, 110, 120, 130, 140, 149]
      4 | person |         89 |       10 |         11.2 |             10 |               0
              Track 4: keyframe ở frame [61, 71, 81, 91, 101, 111, 121, 131, 141, 149]

  Tổng: 73 keyframe / 613 frame có mặt = 11.9 keyframe / 100 frame
  [OK]        Đọc được 5 track từ annotations.xml.

=== Tóm tắt ===
  Lỗi: 0   |   Cảnh báo: 0
  Đạt. Dán phần tóm tắt này vào báo cáo mục 5.
```

![result](image.png)

## 6. Ba câu kết

1. **Nếu được gán lại, bạn sẽ đặt keyframe khác ở đâu? Vì sao?**
   - Ở đối tượng **P4** (Track 2), tôi sẽ phân bổ lại khoảng cách giữa các keyframe đồng đều hơn ở nửa sau đoạn đường (frame 70–140), đồng thời tối ưu hóa các keyframe quá sát nhau ở đoạn đầu (39, 40, 49, 50) bằng cách điều chỉnh góc đón và tỷ lệ phóng đại tổng thể thay vì đặt keyframe dày cục bộ. Điều này giúp giảm số lượng keyframe xuống khoảng 14–16 mà vẫn đảm bảo box không bị trôi và giữ chuyển động nội suy mượt mà hơn.
   - Ở đối tượng **P5**, tôi sẽ bổ sung thêm 1 keyframe trung gian tại frame 102 để định hình box tốt hơn ngay trước khi đối tượng bước vào vùng bị người khác che khuất ở frame 110.

2. **Có chỗ nào bạn không chắc "cùng ID hay ID mới"? Bạn đã quyết định và ghi chú thế nào?**
   - Chỗ nhạy cảm nhất là tại **P1** khi bị che khuất hoàn toàn ở frame 43–49 bởi người đi ngang qua (Person 2): thời điểm đối tượng bắt đầu lộ diện trở lại sau cột kính ở frame 50 có thể gây phân vân liệu đây là cùng người hay một khách hàng khác vừa bước ra khỏi cửa hàng.
   - Tôi đã áp dụng nghiêm ngặt quy tắc 3 câu hỏi của bài học:
     1. Thời gian khuất chỉ có 7 frame (0.23 giây, nhỏ hơn rất nhiều so với ngưỡng 60 frame = 2 giây).
     2. Ngoại hình hoàn toàn trùng khớp (áo len đen, quần tối màu, phom người giống hệt).
     3. Vị trí tái xuất hiện nằm đúng trên đường tịnh tiến vận tốc của P1 từ trái sang phải.
   - Do đó, tôi đã quyết định **giữ cùng Track ID** (tiếp tục Track cũ, tắt cờ `outside` ở frame 50 thay vì tạo track mới) và ghi chú rõ ràng các mốc frame vào mục 3 của báo cáo.

3. **Một lỗi ID trong job của bạn (nếu không còn sót, chọn lỗi đã sửa) có thể làm xe tự lái dự đoán sai chuyển động của người đó như thế nào? Lỗi đó ảnh hưởng MOTA hay IDF1 nhiều hơn?**
   - **Tác động đến xe tự lái:** Nếu để sót lỗi `fragmentation` hoặc `id_switch` ở P1 (ví dụ ngắt track và gán ID mới khi P1 bước ra khỏi chỗ che khuất):
     - Bộ lọc dự đoán chuyển động (Kalman Filter / Motion Forecaster) của xe tự lái sẽ xem đối tượng mới là một người đi bộ vừa bất ngờ xuất hiện từ lề đường thay vì một người đang duy trì vận tốc ổn định.
     - Điều này có thể khiến xe tự lái tính sai vector gia tốc và hướng di chuyển trong tích tắc, dẫn đến phản ứng phanh gấp nguy hiểm (phantom braking) hoặc ngược lại, đánh giá sai thời gian va chạm tiềm tàng (Time-to-Collision).
   - **Ảnh hưởng đến chỉ số:** Lỗi này ảnh hưởng đến **IDF1** nặng hơn rất nhiều so với **MOTA**:
     - *MOTA* đánh giá theo từng frame độc lập (chủ yếu là chất lượng phát hiện Detection: FN, FP và đếm số lần IDSW). Một lần ngắt ID chỉ bị phạt 1 lỗi IDSW trên tổng số hơn 600 box của tập dữ liệu, làm MOTA chỉ giảm một lượng không đáng kể (~0.16%).
     - Trong khi đó, *IDF1* đo lường khả năng duy trì định danh liên tục của toàn bộ quỹ đạo (ID Precision & ID Recall). Khi một track dài 143 frame bị đứt làm đôi thành 2 track nhỏ, chỉ có một nhánh được gán là ID đúng (IDTP), phần còn lại bị tính là lỗi IDFP và IDFN, làm sụt giảm nghiêm trọng điểm số IDF1 của toàn bộ bài làm.

## Bonus (nếu làm)

### B1 — Hai người đi sát

*(Không thực hiện trong bài nộp chính)*

### B2 — So cách đặt keyframe

*(Không thực hiện trong bài nộp chính)*

### B3 — So với tracker có sẵn

*(Không thực hiện trong bài nộp chính)*

## 7. Tài liệu tham khảo và công cụ AI đã dùng

- **Tài liệu tham khảo:**
  - Bộ dữ liệu chuẩn MOTChallenge: `MOT17-09` sequence & benchmark protocol (CC BY-NC-SA 3.0).
  - Cẩm nang thực hành Lab Day 05: Multi-Object Tracking Annotation with CVAT (`docs/GUIDELINE.md`, `docs/MOT_FORMAT.md`, `CHECKPOINTS.md`).
  - Thư viện đánh giá chất lượng tracking chuẩn quốc tế: `py-motmetrics` và `TrackEval`.
- **Công cụ AI hỗ trợ:**
  - Sử dụng Antigravity AI Assistant để kiểm tra đối chiếu dữ liệu xuất từ CVAT, chạy và xác thực output của script kiểm định `src/check_submission.py`, hỗ trợ cấu trúc và định dạng các bảng số liệu trong báo cáo cá nhân.

