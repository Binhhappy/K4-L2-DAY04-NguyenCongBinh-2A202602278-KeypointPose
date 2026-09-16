# Mini guideline -người gán: Nguyễn Công Bình (2A202602278)  |  ngày: 16/09/2026

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống                                                   | Luật nhóm bạn chọn                                                                                                                                                                                                                                                                                                                                                                                     | Vì sao                                                                                                                                                                                                                                                                                                                                                                                         |
| -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hông của người mặc quần áo dài                         | Đặt chấm tại**điểm giữa nếp gấp giữa thân và đùi**, ngang mức cạp quần / thắt lưng, cách trục giữa thân khoảng 1/4 bề rộng vai về mỗi bên. Khớp hông luôn `v = 1` nếu bị áo dài, áo khoác hay vật (xe, bàn) che; chỉ `v = 2` khi thấy rõ cạp quần và đường gấp đùi (vd. `train_16` hai cầu thủ mặc quần đùi).                        | Hông không có bề mặt nhìn thấy. Nếu không quy ước một mốc giải phẫu thì mỗi người đặt một kiểu, sai lệch vài chục pixel giữa hai người gán và model học ra "hông" nằm lung tung dọc thân. Ảnh mẫu:`outputs/vis_train/train_14.jpg` (hai người quay lưng, hông ước lượng theo cạp quần).                                                       |
| Tai bị tóc hoặc mũ bảo hiểm che một phần               | Còn thấy**được vành tai hoặc dái tai** -> `v = 2`. Mũ bảo hiểm kín / tóc phủ hết, nhưng đầu còn trong khung -> `v = 1`, chấm đặt tại **giao giữa đường ngang qua mắt và cạnh bên của hộp sọ**. Mũ lưỡi trai (`train_11`) không che tai -> `v = 2`.                                                                                               | Tai bị che là ca xuất hiện nhiều nhất trong bộ này (17/28`left_ear` là `v = 1`). Không có luật thì người này để `v = 2` ở mũ, người kia để `v = 0` và khớp đó bị loại khỏi OKS luôn. Ảnh mẫu: `outputs/vis_train/train_04.jpg` (hai người đội mũ bảo hiểm kín, tai `v = 1`).                                                              |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Khớp mà**theo tỉ lệ cơ thể nằm ngoài mép ảnh** -> `v = 0`, không đặt chấm (ví dụ gối và cổ chân ở `train_04`, `train_10`). Khớp bị vật che nhưng theo tỉ lệ cơ thể vẫn nằm **trong** khung -> `v = 1` và đặt chấm ước lượng. Hông của người bị cắt ngay dưới thân: đặt chấm sát mép và `v = 1`.                                | Cùng một "không nhìn thấy" nhưng hai nghĩa: ngoài khung thì không có toạ độ hợp lệ, còn bị che thì vẫn có.`check_pose_labels.py` cảnh báo 4 khớp `v = 0` cho những người này - đó là cảnh báo cố ý, không phải lỗi, vì gối/cổ chân thật sự nằm dưới mép dưới. Ảnh mẫu: `outputs/vis_train/train_10.jpg`.                              |
| Cổ tay nằm sau tay lái / sau thân mình                    | Cổ tay khuất sau tay lái xe máy, sau lưng, sau người khác ->`v = 1`, chấm đặt ở **đầu cẳng tay kéo dài** (nối tiếp hướng khuỷu tay). Nếu thấy **bàn tay hoặc găng tay** dù cổ tay bị che -> vẫn `v = 2`, chấm ở gốc bàn tay.                                                                                                                            | Trên xe máy (`train_04`, `train_10`, `train_20`) cổ tay hầu như luôn bị tay lái / găng che một phần. Nếu người gán chỉ dựa vào "có thấy da cổ tay không" thì gần hết cổ tay trong bộ này thành `v = 1`, làm bảng đếm lệch hẳn so với bạn cùng nhóm.                                                                                              |
| Hai người chồng lên nhau                                   | **Làm xong hẳn một người rồi mới sang người kia.** Người đứng trước làm trước. Khớp của người sau bị người trước che -> `v = 1`, chấm đặt theo đối xứng với khớp cùng tên còn thấy được (vai trái che thì lấy vai phải đối xứng qua cột sống). Không kéo chấm sang phần cơ thể của người khác chỉ vì "chỗ đó có một cái vai". | Đây là nguồn của lỗi`nham_nguoi`. Ảnh mẫu: `outputs/vis_train/train_16.jpg` (hai cầu thủ nhảy tranh đĩa, tay chồng lên nhau) và `train_14.jpg` (hai người đứng sát).                                                                                                                                                                                                 |
| Người nhỏ đến mức nào thì không gán nữa             | Gán mọi người mà**chiều cao box >= 1/8 chiều cao ảnh** hoặc còn phân biệt được đầu-vai-hông, **kể cả khi người đó mờ (out-of-focus) hoặc bị cắt ở mép ảnh** - phần ngoài khung gắn `v=0`, phần mờ gắn `v=1`. Chỉ bỏ người nhỏ hơn ngưỡng (người trên du thuyền phía xa trong `train_14`, người đi bộ trong nền `train_19`).     | Bộ ảnh đã được chọn sao cho người chính đủ lớn; người nền cỡ vài chục pixel không thể đặt 17 điểm có nghĩa.**Sửa sau khi chấm gold:** ở `train_13` tôi bỏ người mờ ở mép trái (cao 55% ảnh) vì coi là "nền" -> gold có người đó -> lỗi `thieu_nguoi`. "Mờ" không phải tiêu chí bỏ; chỉ có kích thước mới là tiêu chí. |

Với mỗi luật, ảnh mẫu là ảnh đã vẽ trong `outputs/vis_train/` (sinh bằng
`tools/visualize_pose.py`). Vàng = khớp `v = 1` để thấy ngay khớp nào đã áp luật ước lượng.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_11`, người thứ `1`, khớp `left_knee, right_knee, left_ankle, right_ankle`

- Mơ hồ ở chỗ nào: người đứng sau bàn, từ hông xuống bị bàn và hộp pizza che hoàn toàn.
  Ảnh còn khoảng 80 px phía dưới hông, nên **không rõ gối nằm trong khung (bị che) hay
  ngoài khung**.
- Bạn quyết thế nào: hông `v = 1` (chấm ước lượng sát cạp áo), gối và cổ chân `v = 0`.
- Vì sao: đo tỉ lệ cơ thể - thân từ vai xuống hông dài ~200 px, đùi cũng cỡ đó, nên gối
  nằm ở ~y = 550 trong khi ảnh cao 427 px. Gối chắc chắn ở dưới mép ảnh, không phải chỉ bị bàn che.
- Nếu người khác quyết ngược lại thì model học sai cái gì: nếu đặt gối `v = 1` ở mép bàn
  (y ≈ 350), model học "gối ở ngay dưới hông 1-2 cm" - tỉ lệ đùi bị dạy sai, và ảnh lật
  augmentation dạy sai thêm một lần.

### Ca 2 - ảnh `train_16`, người thứ `2`, khớp `left_shoulder / right_shoulder, left_hip / right_hip`

- Mơ hồ ở chỗ nào: cầu thủ áo đỏ **quay lưng về máy ảnh, đầu ngoảnh sang phải** nhìn đĩa.
  Mặt ở tư thế nghiêng nên mắt trái/phải theo ảnh nằm ngược so với vai trái/phải.
  `check_pose_labels.py` cảnh báo "vai/hông ngược chiều so với hai mắt - dấu hiệu đảo trái/phải".
- Bạn quyết thế nào: giữ nguyên nhãn - tay/chân bên trái cơ thể (xanh) nằm bên trái ảnh vì
  người quay lưng; đầu ngoảnh nên mắt đảo chiều là đúng giải phẫu.
- Vì sao: đứng vào chỗ người đó (lưng về máy ảnh) và giơ tay trái: tay trái xuất hiện bên
  trái ảnh. Heuristic của script chỉ đúng cho người nhìn thẳng; đây là dương tính giả cần ghi
  lại để không "sửa" nhầm.
- Nếu người khác quyết ngược lại thì model học sai cái gì: đổi vai/hông theo mắt sẽ tạo lỗi
  `dao_trai_phai` thật sự trên một ảnh dễ - đúng loại lỗi rubric phạt nặng nhất.

### Ca 3 - ảnh `train_04`, người thứ `1`, khớp `right_wrist`

- Mơ hồ ở chỗ nào: người đi xe máy bên trái, tay phải (bên phải cơ thể = bên trái ảnh) duỗi
  ra cầm tay lái; khuỷu tay ở x ≈ 45 px, cổ tay **nằm đúng mép trái ảnh** (x ≈ 0-10 px), lại
  đeo găng. Không rõ nên coi là "ra ngoài khung" hay "còn trong khung, bị găng che".
- Bạn quyết thế nào: `v = 0`, không đặt chấm.
- Vì sao: kéo thẳng hướng cẳng tay từ khuỷu ra thì điểm cổ tay rơi ngoài x = 0. Phần găng
  còn thấy trong ảnh là ngón tay, không phải cổ tay. Đặt chấm ép vào x = 0 sẽ là một toạ độ bịa.
- Nếu người khác quyết ngược lại thì model học sai cái gì: một cổ tay `v = 1` dính sát mép ảnh
  dạy model rằng cẳng tay ngắn hơn thật; đồng thời làm sai độ dài xương khi tính OKS với gold.

### Ca 4 (thêm) - ảnh `train_14`, người thứ `2`, khớp `left_hip, right_hip, left_knee, right_knee`

- Mơ hồ ở chỗ nào: người ngồi xổm, quay lưng, mặc quần dài tối màu; đùi và bụng chồng lên
  nhau nên nếp gấp hông không thấy.
- Bạn quyết thế nào: hông `v = 1`, đặt tại điểm giữa cạp quần và đường gấp đùi; gối `v = 1` tại
  điểm nhô của đầu gối gập.
- Vì sao: theo luật hông ở mục 2 (mốc cạp quần), và gối gập là điểm cực của đường viền đùi-cẳng chân.
- Nếu người khác quyết ngược lại thì model học sai cái gì: đặt hông ở đầu gối (chỗ nhô ra dễ
  thấy) sẽ làm thân người ngắn lại gần một nửa; model học tư thế ngồi sai tỉ lệ.

## 4. Sau khi so visibility report với bạn cùng nhóm

<!-- Chưa có bài của bạn cùng nhóm tại thời điểm khoá nhãn. Điền sau khi chạy:
python3 tools/visibility_report.py --labels dataset/labels/train \
    --compare ../ban_cung_nhom/dataset/labels/train --markdown reports/visibility_compare.md -->

- Khớp lệch `%v=1` nhiều nhất: `______` (bạn `___%` / họ `___%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**:
- Luật mới bổ sung vào mục 2 sau khi thống nhất:

Dự đoán trước khi so (để kiểm lại sau): khớp lệch nhiều nhất sẽ là **tai** (`left_ear` 61%,
`right_ear` 57% `v = 1` trong bài này) vì luật "mũ bảo hiểm che tai -> `v = 1`" là luật nhóm
tự đặt, không có trong luật cả lớp.
