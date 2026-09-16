# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Nguyễn Công Bình (2A202602278)   Nhóm: ______   Ngày: 16/09/2026

> Số liệu trong mục 1 và 5 lấy trực tiếp từ `reports/visibility_report.md`,
> `outputs/visibility_report.json` và `tools/check_pose_labels.py`; mục 4 lấy từ
> `outputs/eval_model.json` (notebook đã chạy trên Colab); mục 2 từ `outputs/eval_vs_gold.json`
> (cột "trước rework"). Mục 3 và cột "sau rework" chỉ điền được sau khi có bài của bạn cùng nhóm
> và sau khi sửa nhãn; các ô đó đang để trống có chủ ý - không tự ước lượng.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 28 |
| v=2 / v=1 / v=0 | 325 / 124 / 27 |
| Thời gian trung bình mỗi ảnh | ______ phút (tổng ___ phút / 20) |

`check_pose_labels.py`: **ĐẠT định dạng**, 0 lỗi, 5 cảnh báo (xem giải thích cuối mục này).
Export CVAT: `annotations/coco_keypoints/person_keypoints_default.json` - 20 images, 28 annotations,
mỗi mảng `keypoints` đúng 51 số; mỗi dòng YOLO Pose đúng 56 số.

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` - 17/28 = 61%
2. `right_ear` - 16/28 = 57%
3. `left_eye` / `right_eye` - 10/28 = 36% (đồng hạng; kế đến `left_wrist` 32%)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Không hẳn. Tai và mắt có `%v=1` cao vì **hay bị che** (mũ bảo hiểm ở `train_04`, người quay
lưng ở `train_14`, `train_16`, `train_19`, tóc dài ở `train_02`, `train_06`) chứ vị trí của chúng
lại dễ ước lượng: hộp sọ có hình dạng cố định, đặt chấm theo đường ngang qua mắt là xong.
Khớp **khó xác định vị trí giải phẫu** thật sự là hông (`left_hip` 25% `v=1`) và gối của người
ngồi/gập (`train_14` người 2, `train_15`, `train_20`): không có bề mặt nhìn thấy, phải dựa vào
cạp quần và nếp gấp đùi, và mỗi lần đặt tôi đều phải dừng lại đo tỉ lệ thân. Bằng chứng: trong
`outputs/vis_train/`, các chấm vàng ở tai đều bám sát đầu, còn chấm hông ở `train_14.jpg`,
`train_20.jpg` là những chấm tôi kéo lại nhiều lần nhất.

**Về 5 cảnh báo của `check_pose_labels.py`** (đã xem lại từng ảnh, không sửa nhãn):

- `train_04:2`, `train_10:1`, `train_11:1` - "4 khớp v=0 trong khi người nằm gọn giữa ảnh":
  cả ba người đều bị cắt ở mép dưới (đi xe máy, cúi trên xe, đứng sau bàn); gối và cổ chân theo
  tỉ lệ cơ thể nằm **dưới mép ảnh**, nên `v=0` là đúng luật. Xem Ca 1 trong `GUIDELINE_MINI.md`.
- `train_16:2` - "vai/hông ngược chiều so với hai mắt": người quay lưng, đầu ngoảnh sang phải;
  heuristic mắt–vai chỉ đúng cho người nhìn thẳng. Đã kiểm bằng `visualize_pose.py`: hai đường
  vai-hông không cắt chéo, xanh/cam nhất quán. Dương tính giả, xem Ca 2 trong `GUIDELINE_MINI.md`.

## 2. Chấm với gold

Số liệu từ `outputs/eval_vs_gold.json`, chạy ngay khi nhận gold (`gold/gold/labels/train/`):

```bash
python3 tools/evaluate_pose_annotations.py --pred dataset/labels/train \
    --gold gold/gold/labels/train --images dataset/images/train --out outputs/eval_vs_gold.json
```

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9228 | |
| OKS@0.50 | 0.9655 | |
| OKS@0.75 | 0.9655 | |
| Lỗi `dao_trai_phai` | 0 | |
| Lỗi `nham_nguoi` | 1 | |
| Lỗi `xoa_khop_bi_che` | 0 | |

Thêm: `thieu_nguoi` 1, `lech_nhe` 6; người gold 29 / ghép được 28 / thừa 0. Mức: **Xuất sắc**
(qua cổng OKS >= 0.75, OKS@0.75 >= 0.70, không có `dao_trai_phai`). Hai mục chẩn đoán không trừ điểm:
`co_khac_gold` 62 và `gold_khong_gan_nhan` 68 - đúng như README dự báo, gold COCO để `v=0` ở
những khớp tôi gán `v=1` (tai dưới mũ, gối của người bị cắt...).

**Danh sách lỗi phải sửa (theo thứ tự ưu tiên của GUIDE):**

| Ưu tiên | Ảnh | Người | Khớp | Lỗi | Sửa thế nào |
| ---: | --- | ---: | --- | --- | --- |
| 2 | `train_04` | 1 (người lái bên trái) | `left_wrist` | `nham_nguoi`: tôi đặt ở (0.577, 0.773) = găng tay trên tay lái của người bên phải; gold ở (0.505, 0.790) | Kéo chấm sang trái ~45 px về găng tay trái của chính người này, khuất sau tay người bên phải, giữ `v=1` |
| 3 | `train_13` | gold #1 (người mờ ở mép trái, box 0.09/0.61 w0.13 h0.55) | cả skeleton | `thieu_nguoi`: tôi bỏ qua vì coi là người nền | Gán bổ sung 17 điểm: nửa thân trái ra ngoài mép -> `v=0`, phần mờ còn thấy -> `v=1`/`v=2` |
| 6 | `train_12` | 1 | `left_ankle` | `lech_nhe` 94 px (1.5 lần dung sai) | Kéo về đúng mắt cá |
| 6 | `train_02` | 1 | `left_ear` | `lech_nhe` 25 px (2.3 lần) - tai sau mũ bảo hiểm | Dịch chấm ước lượng ra sau, ngang mức mắt |
| 6 | `train_06` | 1 | `right_ear` | `lech_nhe` 19 px (1.0 lần) | Dịch nhẹ |
| 6 | `train_16` | 1 | `nose` | `lech_nhe` 17 px (1.2 lần) | Dịch nhẹ |
| 6 | `train_19` | 2 | `nose`, `right_eye` | `lech_nhe` 8 px / 7 px | Dịch nhẹ (người nhỏ, dung sai chỉ ~5 px) |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

<!-- Điền sau khi rework trong CVAT, export lại, chạy lại coco_kp_to_yolo_pose.py và evaluate. -->

-
-
-

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. Cảnh báo duy nhất của `check_pose_labels.py`
về trái/phải (`train_16:2`, người quay lưng) được gold xác nhận là dương tính giả: skeleton đó
đạt OKS 0.9853, cao nhất bài. Lỗi thật của tôi nằm ở chỗ khác và đều cùng một gốc: **quyết định
"không gán" quá sớm** - bỏ hẳn người mờ ở mép `train_13`, và ở `train_04` chọn cái găng tay dễ
thấy nhất thay vì cái găng bị che của đúng người đang gán. Cả hai đều là ảnh có hai người sát nhau,
không phải ảnh khó về tư thế.

## 3. Kiểm chéo

Bạn cùng nhóm: ______

<!-- Điền sau khi chạy visibility_report.py --compare với bài của bạn cùng nhóm. -->

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_ear` | 61% | | | (dự đoán: guideline - luật "mũ bảo hiểm che tai -> v=1" là luật nhóm tự đặt) |
| `left_hip` | 25% | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

-

## 4. Model

Số liệu chép từ `outputs/eval_model.json` (notebook `day4_pose_finetune_yolo26.ipynb`, Colab T4,
`yolo26n-pose.pt`, 80 epoch, batch 8, imgsz 640, `fliplr=0.5`, seed 20260915; đo trên 10 ảnh test / 13 người).

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

(`box_mAP50`: 0.9785 -> 0.9600, chênh -0.0185.)

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu?** Tăng +0.0055 (0.6853 -> 0.6908), còn `pose_mAP50` đứng
   nguyên ở 0.845 và recall cũng đứng nguyên 0.846 (11/13 người). Nghĩa là model **không tìm thêm được
   người nào**, chỉ đặt chấm **sát khớp hơn một chút** ở những người vốn đã tìm được - đúng dạng cải
   thiện mà 20 ảnh có thể dạy: 12/20 ảnh core là người trên xe máy/xe đạp hoặc bị vật che (`train_04`,
   `train_10`, `train_11`, `train_20`...), gần với `test_01`, `test_03`, `test_05`, `test_09`. Cái nó làm
   hỏng là **box**: `box_mAP50` giảm 0.0185 - 28 box của tôi bao cả tay lái / chân duỗi, được vẽ theo
   khớp chứ không theo thân người như COCO, nên model học ra box hơi khác gold của test. Với chỉ 13
   người trong tập test, một người đổi trạng thái khớp/lệch box là đủ để dịch chỉ số cỡ này; tôi đọc
   đây là "không hại, không lợi rõ rệt", không phải cải thiện thật.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu?** Ở mức 50-95: 0.804 so với 0.691, chênh ~0.11;
   ở mức 50: 0.960 so với 0.845, chênh ~0.115. Model tìm **người dễ hơn tìm khớp**. Box chỉ cần 4 số
   trùng ~50% diện tích là đạt IoU 0.5; pose cần 17 chấm nằm trong bán kính `sigma*s` của từng khớp,
   và một khớp bị che (test có 29 khớp `v=1`, 46 khớp `v=0` trên 13 người) là một cơ hội trượt.
   Recall pose 0.846 = 2/13 người bị bỏ: chính là hai người nhỏ/bị che nhiều (`test_02` người trên bờ
   kè, `test_03` xe sau).

3. **Một ảnh test model đoán sai** (xem `outputs/predictions_test_grid.png`):
   - `test_07` (người phụ nữ sau quầy bánh): thân trên đúng, nhưng model kéo hông/gối/cổ chân
     **xuyên qua mặt bàn xuống dưới**, nối thành một hình chữ nhật dài ra ngoài box. Đây là **trượt
     hẳn** ở nhóm khớp chân - model đoán vị trí "mặc định" của chân khi không nhìn thấy, chứ không
     phải lệch vài pixel.
   - `test_02`: box `person 0.31` ở góc trái là **con chim trên cột** - thừa người (lỗi box, không
     thuộc 4 loại keypoint nhưng làm giảm `box_mAP`).
   - Không thấy lỗi đảo trái/phải hay nhầm người rõ trên 10 ảnh test ở conf 0.25.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của tôi và model?** `train_02` - OKS 0.655 (bảng cuối
   notebook), tiếp theo `train_13` 0.683 và `train_06` 0.711. `train_02` là người đi xe đạp **quay lưng,
   đội mũ bảo hiểm**: tôi gán 5 khớp mặt `v=1` ở phía sau đầu theo luật lớp (bị che, còn trong khung,
   vẫn đặt chấm), còn model - được train trên COCO, nơi khớp không thấy thường là `v=0` - đặt mắt/mũi
   ở chỗ khác hoặc với độ tin cậy thấp. Ở đây **tôi đúng theo guideline của lớp**, model đúng theo
   guideline COCO; con số 0.655 là bất đồng guideline, không phải chấm sai. Bằng chứng: phần vai-hông-
   gối-cổ chân trên `outputs/vis_train/train_02.jpg` thẳng hàng với thân người, không cắt chéo, và ba
   ảnh OKS thấp nhất đều là người **quay lưng hoặc bị che mặt** (`train_02`, `train_06`, `train_13`).

5. **Ảnh tôi gán tệ nhất có cũng là ảnh model đoán tệ nhất không?** Theo gold, ảnh tôi gán tệ nhất
   là `train_13` (thiếu hẳn một người, OKS người đó = 0) rồi `train_02` (0.858) và `train_19` (0.870) -
   và `train_02`, `train_13` **cũng chính là hai ảnh model bất đồng với tôi nhiều nhất** (0.655 và 0.683).
   Ở `train_13` model tìm ra 3 người trong khi tôi gán 2: model đúng, gold cũng có 3. Điều đó nói rằng
   ảnh này khó ở khâu *quyết định ai được gán* (người mờ, bị cắt mép), không khó ở khâu đặt chấm.
   Ảnh tôi tự thấy khó nhất khi gán là `train_14` (hai người quay lưng, một người ngồi xổm) và
   `train_11` (nửa dưới sau bàn), nhưng gold cho 0.975/0.916 và 0.917 - luật hông ở mục 2 guideline hoạt động. `train_14` nằm trong nhóm OKS thấp với model (0.729 / 0.735), còn
   `train_11` thì model và tôi đồng ý cao (0.899). Điều đó nói rằng ảnh khó cho **người** là ảnh phải
   ước lượng khớp không có bề mặt (hông, gối gập), còn ảnh khó cho **model** là ảnh có khớp bị che mà
   guideline hai bên xử lý khác nhau; `train_11` không khó cho model vì tôi để `v=0` cho phần ngoài
   khung nên các khớp đó không được tính. Ngoài ra ở `train_03`, `train_10`, `train_13` model tìm ra
   **nhiều người hơn tôi** (4/2, 2/1, 3/2) - đó là người nền nhỏ mà tôi cố ý bỏ theo luật mục 2 của
   `GUIDELINE_MINI.md`; sẽ đối chiếu với gold xem COCO có gán họ không.

## 5. Một rule evidence bạn đã dùng

**`train_04`, người thứ 2 (người bên phải, áo Fox trắng-đen), khớp `left_knee` / `right_knee`
và hai cổ chân - so với `left_ear` / `right_ear` của cùng người.**

Cả hai nhóm khớp đều "không nhìn thấy", nhưng tôi gán khác nhau. Tai: mũ bảo hiểm kín che hết,
nhưng đầu nằm trọn trong khung và vị trí tai suy ra được từ đường ngang qua hai mắt (thấy rõ qua
kính) và cạnh mũ - còn trong khung, bị che -> `v = 1`, đặt chấm ở cạnh mũ ngang mức mắt. Gối và cổ
chân: hông ước lượng nằm ở y ≈ 0.95 chiều cao ảnh (box người kết thúc ở 0.955), thân từ vai xuống
hông dài ~170 px, nên gối phải nằm ở y > 457 px = dưới mép ảnh. Không có toạ độ hợp lệ nào trong
khung để đặt chấm -> `v = 0`, không đặt chấm. Đây là lý do người này có đúng 4 khớp `v = 0` và
`check_pose_labels.py` cảnh báo; cảnh báo đó tôi giữ nguyên vì nó phản ánh đúng thực tế ảnh.
