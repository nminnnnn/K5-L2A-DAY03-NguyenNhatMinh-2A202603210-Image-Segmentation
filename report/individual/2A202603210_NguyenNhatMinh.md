# Báo cáo thực hành Day 3

- Mã học viên theo lớp: `2A202603210`
- Ngày / CVAT local: 12/11/2026 — CVAT local v2.74.1
- Công cụ đã dùng: CVAT Polygon/Brush, SegFormer-B2 Cityscapes, YOLO11x-seg

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh [LMS](https://aithucchien.vinuni.edu.vn) đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh đầu tiên trong bộ medium_instance, đối tượng là một chiếc ô tô (car) nằm trên mặt đường, ở khu vực phía dưới của ảnh. Tôi sử dụng công cụ Polygon trong CVAT để vẽ thủ công đường viền đối tượng.

Class và quy tắc tôi dùng để chọn biên: Tôi chọn class car và xác định đường biên dựa trên hình dạng thực tế của xe. Mask chỉ bao gồm phần xe nhìn thấy được, không lấy thêm pixel thuộc mặt đường, bóng đổ hoặc các đối tượng xung quanh. Tại những vùng bị che khuất hoặc khó phân biệt, tôi dừng đường biên tại phần nhìn thấy thay vì tự suy đoán.

Nếu dùng gợi ý sau đó: Không dùng gợi ý tự động cho đối tượng này.

Nếu không dùng gợi ý: Không dùng. Tôi chọn vẽ thủ công vì đường viền của xe tương đối rõ ràng, có thể xác định bằng Polygon. Cách này giúp tôi chủ động kiểm soát đường biên, hạn chế gán nhầm pixel nền và đảm bảo mỗi chiếc xe được tạo thành một instance riêng biệt.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: `cp6_coverage`, ảnh `7daa6479-67988f3f.jpg`, vùng chiếc bus lớn và ranh giữa tòa nhà với vỉa hè.
- Lỗi thuộc loại: sai lớp, biên và phủ vùng.
- Bằng chứng tôi nhìn thấy: kết quả auto-label Panoptic bị lem mask `building` lên các pixel của chiếc bus; một phần mask `building` cũng tràn xuống vùng `sidewalk`. Bus không có trong danh sách label của `cp6_coverage`, nên việc phủ `building` lên bus là sai.
- Quy tắc và hành động sửa: tôi chỉ giữ mask trên phần bề mặt thật sự nhìn thấy của từng lớp. Tôi dùng Eraser loại bỏ phần `building` phủ lên bus, để bus ở trạng thái không gán nhãn vì class `bus` không có trong schema; sau đó sửa lại ranh `building`–`sidewalk` theo mép công trình và bề mặt dành cho người đi bộ.
- Sau sửa đã Save và export lại chưa? Đã Save trong CVAT, export lại `cp6_coverage.zip` theo định dạng Segmentation mask 1.1 và kiểm tra lại file.

Kết quả tự kiểm tra: `python scripts/inspect_submissions.py` báo `[OK] cp6_coverage: cp6_coverage.zip`; đây chỉ là kiểm tra cấu trúc file, chưa có điểm metric. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `medium_instance` – ảnh `000000181542.jpg`, các hình người xuất hiện trong gương/kính và hình quảng cáo ở hậu cảnh | Có thể gán `person` vì vẫn nhìn thấy hình dáng con người; hoặc không gán vì đó chỉ là ảnh/phản chiếu, không phải một người thật đang hiện diện trong cảnh | Tôi phân biệt người thật với hình người được thể hiện gián tiếp qua poster, ảnh hoặc phản chiếu. Các vùng này không có bằng chứng chắc chắn là một instance người thật trong không gian của cảnh | Tôi tạm thời không đánh dấu `person` cho các hình trong gương/ảnh và muốn hỏi coach: trong bộ quy tắc của bài, người chỉ xuất hiện qua phản chiếu hoặc hình quảng cáo có được tính là một instance `person` không? |
| `cp6_coverage` – ảnh `7daa6479-67988f3f.jpg`, chiếc bus lớn ở giữa ảnh | Có thể gán bus thành `car` vì đều là phương tiện; hoặc để không gán nhãn vì schema không có class `bus` | `cvat-labels.json` của task chỉ có `road`, `sidewalk`, `building`, `vegetation`, `sky`, `car`, `person`; không được ép một vật ngoài schema vào lớp gần giống | Tôi để bus không gán nhãn và xóa các phần mask `car`/`building` bị lem lên bus |
| `cp4_curb` – ảnh `7d83710e-4697c3b2.jpg`, vùng driveway cắt qua vỉa hè bên phải | Có thể hiểu phần bê tông là road vì xe có thể đi qua; hoặc là sidewalk vì tuyến đi bộ vẫn tiếp tục qua driveway | Tôi dựa vào chức năng bề mặt và đường curb thay vì chỉ dựa vào màu; driveway không tự động làm mất tính liên tục của sidewalk | Tôi giữ phần tuyến đi bộ là `sidewalk`, phần mặt đường xe chạy là `road`, rồi chỉnh biên theo curb nhìn thấy |
