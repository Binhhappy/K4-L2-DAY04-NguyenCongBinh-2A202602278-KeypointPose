# Kiểm chéo - lỗi tìm được trong bài của bạn cùng nhóm

Người gán (bài được kiểm): ______   Người kiểm: Nguyễn Công Bình   Ngày: 16/09/2026

> Chưa nhận được bài của bạn cùng nhóm tại thời điểm khoá nhãn. Phần dưới là checklist đã
> chuẩn bị và các lệnh sẽ chạy; bảng lỗi điền khi có `../ban_cung_nhom/dataset/labels/train`.

## Lệnh đã/sẽ chạy trước khi soi bằng mắt

```bash
python3 tools/check_pose_labels.py --images dataset/images/train --labels ../ban_cung_nhom/dataset/labels/train
python3 tools/visualize_pose.py --images dataset/images/train --labels ../ban_cung_nhom/dataset/labels/train --out outputs/vis_review
python3 tools/visibility_report.py --labels dataset/labels/train \
    --compare ../ban_cung_nhom/dataset/labels/train --markdown reports/visibility_compare.md
```

## Reviewer checklist (chép từ `reports/REVIEWER_CHECKLIST.md`)

| | Mục kiểm | Đạt? | Ghi chú / ảnh nào |
| --- | --- | --- | --- |
| 1 | Mọi người trong ảnh đều có đủ 17 điểm, không ai bị thiếu | ☐ | |
| 2 | Bật đường nối: không có xương nào cắt chéo ở vai hoặc hông | ☐ | |
| 3 | Không có xương nào kéo dài sang một cơ thể khác | ☐ | |
| 4 | Khớp bị che dùng `v = 1` **và có chấm**, không phải `v = 0` | ☐ | |
| 5 | `v = 0` chỉ xuất hiện ở khớp thật sự ra ngoài mép ảnh | ☐ | |
| 6 | Không có dấu hiệu dùng `Hidden` (điểm `v = 2` nằm ở chỗ vô lý) | ☐ | |
| 7 | Export đúng **COCO Keypoints 1.0**: mảng `keypoints` có 51 số mỗi người | ☐ | |
| 8 | Bản YOLO Pose: mỗi dòng 56 số, `kpt_shape: [17, 3]` | ☐ | |
| 9 | Visibility report đã nộp, và hai bảng đã được đặt cạnh nhau | ☐ | |
| 10 | Mọi ca không rõ đều được ghi trong `GUIDELINE_MINI.md` | ☐ | |
| 11 | `check_pose_labels.py` chạy 0 lỗi | ☐ | |

## Lỗi tìm được

Mỗi dòng một lỗi, đủ bốn cột - người sửa phải mở đúng chỗ đó được mà không cần hỏi lại.

| Ảnh | Người thứ | Khớp | Lỗi gì | Sửa thế nào |
| --- | ---: | --- | --- | --- |
| | | | | |
| | | | | |
| | | | | |

Những chỗ sẽ soi đầu tiên (rút từ chính bài của tôi):

- `train_04`, `train_10`, `train_11`: gối/cổ chân của người bị cắt mép dưới - họ để `v=0` hay `v=1`?
  Nếu khác tôi, đây là bất đồng guideline "ngoài khung vs bị che", không phải gán sai.
- `train_16` người 2 (quay lưng): có bị "sửa" trái/phải theo cảnh báo mắt–vai của script không?
- `train_14`, `train_20`: vị trí hông của người ngồi/quay lưng - lệch bao nhiêu so với mốc cạp quần.
- Tai dưới mũ bảo hiểm (`train_04`): `v=1` hay `v=2`/`v=0`.

## Hai câu kết luận

- Lỗi lặp đi lặp lại nhiều nhất của bài này:
- Nó là lỗi **thao tác** hay lỗi **guideline chưa rõ**?
