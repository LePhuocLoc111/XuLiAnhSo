import cv2
import numpy as np
import matplotlib.pyplot as plt
import os

cv2: Thư viện OpenCV để xử lý ảnh.

numpy: Dùng để thao tác ma trận ảnh.

matplotlib.pyplot: Dùng để hiển thị ảnh trong notebook.

os: Dùng để kiểm tra sự tồn tại của file

Bài 1

img = cv2.imread('exercise/colorful-ripe-tropical-fruits.jpg')

Đọc ảnh tổng hợp nhiều loại trái cây.

x, y, w, h = 1240, 400, 140, 140
kiwi = img[y:y+h, x:x+w]

Lấy vùng chứa quả kiwi bằng cách cắt theo tọa độ đã chọn.
Tịnh tiến ảnh kiwi


tx, ty = 50, 30
translated = cv2.warpAffine(kiwi, ...)

Dời ảnh kiwi sang phải 50 px, xuống dưới 30 px bằng ma trận tịnh tiến.



def apply_wave_effect(...):
    ...
    map_x += amplitude * np.sin(map_y * frequency)

Biến dạng ảnh theo kiểu sóng nhấp nhô ngang:
Biên độ: 10 px
Tần số: dựa trên chiều cao ảnh
Dùng cv2.remap để tạo hiệu ứng lượn sóng.



plt.imshow(...)
cv2.imwrite('exercise/kiwi_wave.jpg', wave_img)

Hiển thị ảnh kiwi đã biến dạng.
Lưu ảnh kết quả vào thư mục exercise.



Bài 2

du_du_path = "exercise/DuDu.jpg"
dua_hau_path = "exercise/DuaHau.jpg"

if not os.path.exists(du_du_path):
    print(...)  # Báo lỗi nếu không có ảnh
    exit()

Kiểm tra xem file ảnh có tồn tại không trước khi đọc.



img_papaya = cv2.imread(du_du_path)
img_watermelon = cv2.imread(dua_hau_path)

img_papaya = cv2.resize(img_papaya, (300, 300))
img_watermelon = cv2.resize(img_watermelon, (300, 300))

Đọc ảnh bằng OpenCV (cv2.imread trả về ảnh dạng BGR).
Resize về kích thước chuẩn để dễ xử lý.
grad_papaya = np.zeros_like(img_papaya)

for y in range(300):
    r = 255 - int(255 * y / 299)
    g = int(255 * y / 299)
    grad_papaya[y, :] = [0, g, r]  # BGR

Tạo ma trận màu grad_papaya có cùng kích thước.
Với mỗi hàng y, tính dải màu từ đỏ (r = 255, g = 0) đến xanh lá (r = 0, g = 255).
Mỗi hàng mang một màu khác nhau tạo thành gradient theo chiều dọc.


colored_papaya = cv2.addWeighted(img_papaya, 0.3, grad_papaya, 0.7, 0)

Trộn ảnh đu đủ gốc với gradient màu: 30% ảnh gốc + 70% màu gradient.



grad_watermelon = np.zeros_like(img_watermelon)
for x in range(300):
    b = int(255 * x / 299)
    g = 255 - int(255 * x / 299)
    grad_watermelon[:, x] = [b, g, 255]  # BGR

Gradient màu vàng → tím (vàng: [0,255,255], tím: [255,0,255]).
Tạo gradient theo chiều ngang.


colored_watermelon = cv2.addWeighted(img_watermelon, 0.3, grad_watermelon, 0.7, 0)

Trộn ảnh dưa hấu gốc với màu gradient tương tự như bên đu đủ.

canvas = np.zeros((320, 640, 4), dtype=np.uint8)

Tạo canvas kích thước 320x640, 4 kênh (RGBA).
dtype=np.uint8: giá trị pixel từ 0–255.
Ảnh này ban đầu đen và hoàn toàn trong suốt.


canvas[10:310, 10:310, :3] = colored_papaya
canvas[10:310, 10:310, 3] = 255  # Alpha đầy đủ

Gán ảnh đu đủ vào bên trái và làm cho vùng đó không trong suốt (alpha = 255).



canvas[10:310, 330:630, :3] = colored_watermelon
canvas[10:310, 330:630, 3] = 255

Gán ảnh dưa hấu vào bên phải canvas.


plt.imshow(cv2.cvtColor(canvas, cv2.COLOR_BGRA2RGBA))
plt.title("Đu đủ & Dưa hấu Gradient (Nền trong suốt)")
plt.axis("off")
plt.show()

Chuyển ảnh từ BGR-A (OpenCV) sang RGBA (matplotlib).
Hiển thị ảnh gradient có nền trong suốt.


save_path = "exercise/du_du_dua_hau_gradient.png"
cv2.imwrite(save_path, canvas)
print(...)

Lưu ảnh ở định dạng PNG để giữ nền trong suốt.

Bài 3

import cv2, numpy, matplotlib.pyplot, scipy.ndimage.rotate, os

Thư viện dùng cho xử lý ảnh, ma trận, hiển thị và xoay ảnh.



mountain_path = "exercise/mountain.jpg"
boat_path = "exercise/boat.jpg"

Nếu ảnh không tồn tại → in lỗi và dừng chương trình.



img_mountain = cv2.resize(cv2.imread(mountain_path), (300, 300))
img_boat = cv2.resize(cv2.imread(boat_path), (300, 300))

Cả 2 ảnh được resize về kích thước 300x300 để dễ ghép.

rotate(img, angle=45, reshape=False)

Xoay ảnh 45°, giữ nguyên khung ảnh ban đầu.
Dùng scipy.ndimage.rotate.


cv2.flip(image, 0)
0 là chiều dọc (trên ↔ dưới).

Tạo hiệu ứng như ảnh bị lật gương theo trục ngang.


Chỉnh sửa
canvas = np.ones((320, 640, 3), dtype=np.uint8) * 255

Kích thước đủ chứa 2 ảnh (cộng thêm padding).
Ảnh nền trắng, 3 kênh màu (RGB).


canvas[10:310, 10:310] = mountain_mirror
canvas[10:310, 330:630] = boat_mirror

Ảnh núi ở bên trái, ảnh thuyền ở bên phải.
Cách mép 10 pixel.


plt.imshow(...)
cv2.imwrite("exercise/mountain_boat_mirror.jpg", canvas)

Hiển thị ảnh sau xử lý.
Lưu vào file JPG.

Bài 4

pagoda_path = "exercise/pagoda.jpg"
if not os.path.exists(pagoda_path):
    ...
img = cv2.imread(pagoda_path)

Kiểm tra xem ảnh chùa có tồn tại không.
Nếu có → đọc ảnh bằng OpenCV.


zoomed = cv2.resize(img, None, fx=5, fy=5, interpolation=cv2.INTER_CUBIC)

fx=5, fy=5: phóng to chiều ngang và dọc lên 5 lần.
INTER_CUBIC: nội suy chất lượng cao (giữ mịn ảnh).

map_y, map_x = np.indices((h, w), dtype=np.float32)

Tạo ma trận chứa vị trí từng pixel (tọa độ gốc của ảnh).
Dùng để biến đổi tọa độ sau này.


amplitude = 30
frequency = 2 * np.pi / w
map_y += amplitude * np.sin(map_x * frequency)

Uốn cong ảnh theo trục y bằng hàm sin(x):
Biên độ = 30 pixel (mức độ cong).
Tần số dựa trên chiều rộng ảnh.


warped = cv2.remap(zoomed, map_x, map_y, ...)

Hàm remap sẽ lấy từng pixel ở vị trí cũ và đặt vào vị trí mới (theo map_x, map_y).
Kết quả: ảnh bị uốn cong sóng ngang.

plt.imshow(...)
cv2.imwrite('exercise/pagoda_warped.jpg', warped)

Hiển thị ảnh bị biến dạng bằng matplotlib.
Lưu ảnh kết quả ra file .jpg.
Bài 5

image_files = { "1": ..., "2": ..., "3": ... }
transform_names = { "1": "Tịnh tiến", "2": "Xoay", ... }
os.makedirs("exercise/results", exist_ok=True)

Khai báo:
Danh sách ảnh có sẵn
Tên các hiệu ứng biến đổi
Tạo thư mục results để lưu kết quả nếu chưa có


print("Chọn ảnh:")
...
img_choice = input(...)

In danh sách ảnh
Nhận số từ người dùng
Nếu ảnh không hợp lệ → thoát


print("Chọn phép biến đổi:")
...
transform_choice = input(...)

Hiển thị menu 5 lựa chọn xử lý ảnh:

1: Tịnh tiến

2: Xoay

3: Zoom

4: Làm mờ Gaussian

5: Hiệu ứng sóng

 Tịnh tiến ảnh
M = np.float32([[1, 0, dx], [0, 1, dy]])
result = cv2.warpAffine(img, M, (w, h))

Xoay ảnh
result = rotate(img, angle=angle, reshape=reshape, ...)

Zoom ảnh
result = cv2.resize(img, None, fx=zoom, fy=zoom)

Làm mờ Gaussian
ksize = int(6 * sigma + 1)
result = cv2.GaussianBlur(img, (ksize, ksize), ...)

Hiệu ứng sóng
map_y += amplitude * np.sin(map_x * frequency)
result = cv2.remap(img, map_x, map_y, ...)
Nếu người dùng nhập sai phép biến đổi → in lỗi và thoát.

plt.subplot(1, 2, 1)
plt.imshow(img_rgb)
...
plt.subplot(1, 2, 2)
plt.imshow(result_rgb)

Hiển thị ảnh gốc và kết quả song song
Có tiêu đề cho từng ảnh


cv2.imwrite(save_path, result)

Lưu ảnh đã biến đổi vào thư mục exercise/results/
Tên file có hậu tố là loại hiệu ứng (vd: quang_ninh_warp.jpg)
