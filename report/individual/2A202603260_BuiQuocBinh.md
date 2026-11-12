# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên [LMS](https://aithucchien.vinuni.edu.vn). Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](../../reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: `2A202603260` 
- Ngày / CVAT local: `12/11/2026` / `http://localhost:8080/` 
- Công cụ đã dùng: CVAT local (`Polygon`, `Brush`), script tự kiểm cấu trúc (`scripts/inspect_submissions.py`) và bộ chấm đối chiếu reference 3 tier (`scoring/score.py`, `scoring/scorecard.py`); không dùng SAM.

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh [LMS](https://aithucchien.vinuni.edu.vn) đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `submissions/easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `submissions/medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `submissions/hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `submissions/cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `submissions/cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `submissions/cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `submissions/cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `submissions/cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `submissions/cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | **14 / 14** | **100** |

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach: Không có lỗi export; cả 9/9 file ZIP trong `submissions/` đều xuất đúng định dạng quy định (`Segmentation mask 1.1` cho 4 task semantic và `COCO 1.0` cho 5 task instance/panoptic) và đều qua kiểm tra cấu trúc `[OK]` bằng `scripts/inspect_submissions.py`.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg` — object đầu tiên theo thứ tự ID trong file export là `person` đứng trên vỉa hè phía xa trước cửa hàng Chanel (`ann_id=1`, `bbox=[363.2, 122.3, 9.4, 18.4]`, `area=102 px`, đạt `IoU = 0.775` so với GT) và object tiền cảnh chính tự vẽ thủ công bằng Polygon là người phụ nữ mặc áo dài trắng đang băng qua vạch kẻ đường ở chính giữa ảnh (`ann_id=18`, `bbox=[187.8, 130.4, 123.0, 332.4]`, `area=24,613 px`, đạt `IoU = 0.976` so với GT).
- Class và quy tắc tôi dùng để chọn biên: Class `person`. Quy tắc chọn biên: dùng công cụ `Polygon` chấm điểm bám sát đường viền cơ thể và tà áo dài trắng **nhìn thấy được**; dừng mask ngay tại mép mũi dép tiếp xúc mặt đường (không tô tràn xuống phần bóng đổ xám trên vạch sơn trắng) và cắt đúng ranh giới nơi tà áo dài che khuất phần đầu chiếc xe máy chạy ngang phía sau (`ann_id=9`, class `motorcycle`, `IoU = 0.987`) để hai instance tiếp giáp khít mà không chồng lấn hay tự đoán phần xe bị che.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Khi thử công cụ hỗ trợ viền/Brush cho các người nhỏ ở hậu cảnh sát mép phải vỉa hè (`ann_id=17`, `bbox=[594.7, 126.8, 18.0, 31.9]`, sinh ra 53 đỉnh polygon), biên tự động dễ bị răng cưa, ăn lấn sang nền tối hoặc gộp các người đứng sát nhau trước cửa hiệu (`ann_id=1, 2, 3, 5, 11`). Do đó tôi chuyển sang chỉnh/vẽ thủ công bằng `Polygon` ở mức zoom lớn để kiểm soát từng đỉnh và tách rời từng cá thể.
- Nếu không dùng gợi ý: Toàn bộ 72/72 annotation trong `submissions/medium_instance.zip` đều được kiểm soát và xuất dưới dạng `polygon` tách biệt từng instance.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng:
  1. Trên `medium_instance` (ảnh `000000181542.jpg` vùng giao cắt giữa người mặc áo dài `ann_id=18` với xe máy `ann_id=9`, cụm người lái `ann_id=19` trên xe máy `ann_id=21`, và ảnh `000000373353.jpg` vùng người đi bộ nhỏ phía xa sau taxi vàng `ann_id=44` tại `bbox=[380.2, 303.6, 5.6, 22.5]`).
  2. Trên `easy_semantic` (ảnh `81ae7cbb-6bc63a4a.jpg` vùng vỉa hè bê tông hẹp nằm sau dải cỏ/tuyết hai bên đường + vùng cành cây khô mùa đông chồng lên mặt nhà; ảnh `7ee6d192-89e2408b.jpg` vùng cỏ khô màu nâu trên hai sườn đồi).
- Lỗi thuộc loại: Biên & tách-gộp vật bị che khuất (`medium_instance`, `hard_panoptic`) và thiếu vùng phủ / nhầm lớp tại vùng chuyển tiếp (`sidewalk`, `vegetation` ở `easy_semantic`).
- Bằng chứng tôi nhìn thấy:
  - Khi kiểm tra danh sách Objects trong CVAT và đối chiếu với bộ ground truth 3 tier (`tiers_gt.zip`):
    - Ở `medium_instance`: Ban đầu tại vùng giao cắt giữa người và xe máy (`000000181542.jpg`), vẽ nhanh dễ làm mask xe máy đè lên tà áo dài hoặc gộp người ngồi trên xe vào mask xe. Sau khi tách riêng từng polygon theo phần nhìn thấy, bài làm khớp **70 / 71 instance** của GT (`mean_matched_IoU = 0.942`, `Recall@0.5 = 0.986`, `Precision@0.5 = 0.972`); chỉ còn 1 người đi bộ ở rất xa phía sau taxi vàng trong ảnh `000000373353.jpg` (`ann_id=44`, diện tích 71 px so với GT 164 px) bị vẽ biên hẹp ở phần chân tối màu nên đạt `IoU = 0.424` (hụt nhẹ dưới ngưỡng `0.50`), và thừa 1 `person` bị che ở mép phải ảnh `000000181542.jpg` (`ann_id=17`, 290 px).
    - Ở `easy_semantic`: Đối chiếu per-class IoU cho thấy `road` (`0.980`), `sky` (`0.969`) và `building` (`0.842`) đạt độ khớp rất cao, nhưng `sidewalk` (`0.383`) và `vegetation` (`0.685`) thấp hơn do ở ảnh `81ae7cbb-6bc63a4a.jpg` dải vỉa hè bê tông mảnh nằm sau lớp cỏ/tuyết (`36,747 px`, chiếm 3.99% ảnh) bị bỏ trống `unlabeled` (`sidewalk IoU = 0.000` ở ảnh này, trong khi ảnh `817bca71-00000000.jpg` đạt `sidewalk IoU = 0.733`), đồng thời vùng cành cây khô trụi lá phía trước nhà (`15,096 px`) bị tô gộp vào `building`; còn ở ảnh `7ee6d192-89e2408b.jpg` phần cỏ khô màu nâu vàng trên sườn đồi (`88,727 px`, 9.63% ảnh) bị để trống vì nhìn giống đồi đất trống (`vegetation IoU = 0.347` ở ảnh này).
- Quy tắc và hành động sửa: Áp dụng quy tắc chỉ vẽ phần nhìn thấy, phóng to để tách biên giữa `person` và `motorcycle`/`car`, kiểm tra ranh giới chức năng `road`–`sidewalk` theo bó vỉa (đặc biệt rút kinh nghiệm áp dụng kỹ ở `cp4_curb` với `sidewalk` 23.17%, `road` 14.57% và `cp6_coverage` phủ đủ 7 lớp đạt 85.95% diện tích ảnh).
- Sau sửa đã Save và export lại chưa? Đã **Save** trên CVAT, xuất lại đầy đủ 9 file ZIP vào `submissions/` và kiểm tra hợp lệ bằng `inspect_submissions.py`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có):
- **`easy_semantic`**: `mIoU = 0.772`, `coverage = 93.1%` (`road: 0.980`, `sky: 0.969`, `building: 0.842`, `vegetation: 0.685`, `sidewalk: 0.383`) → **16.5 / 20.0 điểm**.
- **`medium_instance`**: `metric (mean_matched_IoU × recall) = 0.929` (`mean_matched_IoU = 0.942`, `P@0.5 = 0.972`, `R@0.5 = 0.986`, `TP = 70, FP = 2, FN = 1`, `submitted = 72 vs GT = 71`) → **32.0 / 32.0 điểm**.
- **`hard_panoptic`**: `PQ = 0.688` (`SQ = 0.780`, `RQ = 0.806`; đạt `RQ = 1.000` tuyệt đối ở 8/12 lớp: `building`, `bus` 3/3, `car` 26/26, `motorcycle` 4/4, `sky`, `traffic light` 4/4, `truck` 4/4, `vegetation`) → **30.0 / 30.0 điểm**.
- **Tổng scorecard tự đánh giá 3 tier:** **78.5 / 82.0 điểm**.

Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `hard_panoptic` — ảnh `000000460147.jpg`, phần lòng đường chiều bên trái (`x=127..420, y=138..424`) nơi xe con trắng và xe tải chở ô tô đang lưu thông | **Cách 1:** Gán cả hai chiều lòng đường nhựa có xe chạy là `road`, chỉ gán phần lề bộ hành/bãi đỗ phía ngoài hàng rào sắt bên trái và vỉa hè bên phải là `sidewalk`.<br>**Cách 2 (theo GT `panoptic.json`):** Chỉ gán làn đường chiều bên phải (`11,824 px`) là `road`, còn toàn bộ lòng đường chiều bên trái + dải phân cách cây xanh (`63,310 px`) gán là `sidewalk`. | Theo quy tắc chức năng (`guideline-mini-sheet.md`), ranh `road`–`sidewalk` xác định theo công năng sử dụng và bó vỉa/rào chắn: phần đường bên trái có vạch phân làn đứt nét, chữ kẻ mặt đường giao thông và xe tải/ô tô đang chạy, ngăn cách với lề bộ hành bằng hàng rào kim loại. | Tôi quyết định vẽ theo **Cách 1** (`road = 52,658 px`, `sidewalk = 17,827 px`). Khi đối chiếu GT, sự khác biệt này làm `pred_road` trùng với `gt_sidewalk` (`IoU = 0.560`) và chỉ trùng `gt_road` `IoU = 0.196` ở ảnh `000000460147` (dù ảnh `000000350023` đạt `road IoU = 0.911`). Xin hỏi coach: nhãn `sidewalk` phủ lên làn xe chạy bên trái trong GT ảnh `000000460147` có phải là nhãn nhiễu từ bộ dữ liệu gốc không ạ? |
| 2. `easy_semantic` — ảnh `7ee6d192-89e2408b.jpg` (hai sườn đồi màu nâu vàng hai bên cao tốc) và ảnh `81ae7cbb-6bc63a4a.jpg` (vùng cành cây khô trụi lá mùa đông đan trước mặt tiền nhà và dải vỉa hè mảnh sau tuyết) | **Cách 1:** Chỉ gán `vegetation` cho các lùm cây xanh rõ trên đỉnh đồi, để trống (`unlabeled`) sườn đồi màu nâu vì nghi là đất trống (`terrain`), và gán `building` cho mặt tường nhà nhìn xuyên qua các cành cây khô mảnh.<br>**Cách 2 (theo GT):** Tô kín toàn bộ hai sườn đồi cỏ khô và toàn bộ vùng tán cành cây khô trước nhà là `vegetation`, đồng thời tô dải bê tông mảnh sau tuyết là `sidewalk`. | Phiếu quy tắc nêu *"không ép đoán cho đủ coverage"* khi không chắc. Ảnh `7ee6d192-89e2408b` chụp thiếu sáng làm cỏ khô tiệp màu đất đồi; ảnh `81ae7cbb-6bc63a4a` có cành cây mùa đông rất mảnh nhìn xuyên thấy rõ tường và cửa sổ nhà phía sau. | Tôi đã chọn **Cách 1** khi tự làm (dẫn tới lệch `88,727 px` `vegetation` chưa tô ở `7ee6d192-89e2408b`, `15,096 px` `vegetation` tô thành `building` và `36,747 px` `sidewalk` sau băng tuyết chưa tô ở `81ae7cbb-6bc63a4a`). Câu hỏi cho coach: với cành cây khô thưa nhìn xuyên thấy toàn bộ cấu trúc tòa nhà phía sau, quy chuẩn lớp ưu tiên tô phủ cả vòm cành là `vegetation` hay ưu tiên lớp nền `building` nhìn thấy qua kẽ cành? |
| 3. `hard_panoptic` (`000000350023.jpg`) & `cp1_holes` (`000000144300.jpg`) — cụm người + xe đạp siêu nhỏ ở xa (`bbox=[387, 357, 6, 15]`, ~54 px) và vùng kính chắn gió xe mô tô che phía trước xe buýt/xe tải | **Cách 1:** Ở `000000350023.jpg`, vẽ một mask `bicycle` (`id=22`, 54 px) bao quanh cụm xe đạp ở xa; ở `cp1_holes`, giữ liền phần kính chắn gió trong suốt nằm trong mask `motorcycle` (`id=7`) mà không khoét lỗ.<br>**Cách 2:** Tách cụm 6×15 px ở xa thành 2 mask siêu nhỏ `person` (34 px) + `bicycle` (31 px) riêng biệt; ở `cp1_holes`, khoét bỏ phần kính trong suốt của mô tô để lộ `bus`/`truck` phía sau. | Quy tắc `cp1_holes` nêu rõ kính/khe trên xe thuộc về mask vật, không khoét lỗ tùy tiện. Còn ở `hard_panoptic` (`000000350023.jpg`), do cụm người đi xe đạp ở quá xa (chỉ rộng 6 px), việc mask `bicycle` (`id=22`) ăn lấn vài pixel lên chân người đạp làm `IoU` với GT `bicycle` đạt `0.491` (chỉ thiếu `0.009` để đạt ngưỡng `TP > 0.50`). | Ở `cp1_holes`, tôi quyết định giữ nguyên kính chắn gió trong mask `motorcycle` theo đúng quy tắc trạm. Với các vật siêu nhỏ (< 50–60 px ở `hard_panoptic` và `medium_instance` như `person` `ann_id=44` đạt `IoU = 0.424` hay `bicycle` `id=22` đạt `IoU = 0.491`), khi lệch 1–2 pixel biên đã làm IoU rơi sát dưới ngưỡng `0.50`, coach khi review có xem xét châm chước cho các instance kích thước cực nhỏ này không ạ? |
