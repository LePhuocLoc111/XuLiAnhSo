import cv2
import numpy as np
import matplotlib.pyplot as plt

cv2: Thư viện OpenCV, dùng để xử lý ảnh.
numpy: Xử lý mảng, dữ liệu ảnh được lưu dưới dạng mảng NumPy.
matplotlib.pyplot: Thư viện vẽ biểu đồ, dùng để hiển thị ảnh.


Bài 1

img = cv2.imread('exercise/colorful-ripe-tropical-fruits.jpg')
if img is None:
    print("❌ Không đọc được ảnh.")
    exit()


cv2.imread(...): Đọc ảnh từ đường dẫn.
img is None: Kiểm tra nếu ảnh không tồn tại hoặc đọc lỗi, thì in cảnh báo và thoát chương trình.

x, y, w, h = 130, 220, 100, 100
Xác định tọa độ của vùng hình chữ nhật bao quanh quả kiwi.
x, y: Tọa độ góc trên bên trái.
w, h: Chiều rộng và chiều cao của vùng.

kiwi = img[y:y+h, x:x+w].copy()

Lấy phần ảnh chứa quả kiwi bằng slicing ([y:y+h, x:x+w]).
.copy(): Tạo bản sao riêng biệt (nếu không có thì sẽ thay đổi cả ảnh gốc).

img_no_kiwi = img.copy()
img_no_kiwi[y:y+h, x:x+w] = 0

Tạo bản sao img_no_kiwi từ ảnh gốc.
Thay toàn bộ vùng kiwi cũ bằng màu đen (0 = [0,0,0] = đen trong BGR).

new_x = x + 30
img_no_kiwi[y:y+h, new_x:new_x+w] = kiwi

Tăng tọa độ x thêm 30 để dịch sang phải.
Dán phần kiwi đã cắt vào vị trí mới trong ảnh sao chép (img_no_kiwi).

cv2.rectangle(img, (x, y), (x+w, y+h), (255, 0, 0), 2)          # Khung xanh vùng gốc
cv2.rectangle(img_no_kiwi, (new_x, y), (new_x+w, y+h), (0, 0, 255), 2)  # Khung đỏ vùng mới


cv2.rectangle(...): Vẽ hình chữ nhật.

Vùng gốc: viền màu xanh dương (255, 0, 0).
Vùng mới (sau khi tịnh tiến): viền màu đỏ (0, 0, 255).

fig, axs = plt.subplots(1, 2, figsize=(12, 6))
axs[0].imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
axs[0].set_title('Ảnh gốc có khung kiwi')
axs[0].axis('off')
axs[1].imshow(cv2.cvtColor(img_no_kiwi, cv2.COLOR_BGR2RGB))
axs[1].set_title('Sau khi tịnh tiến kiwi sang phải 30px')
axs[1].axis('off')
plt.tight_layout()
plt.show()


Tạo 2 ô hiển thị cạnh nhau (1 hàng, 2 cột).
cv2.cvtColor(..., cv2.COLOR_BGR2RGB): Chuyển ảnh từ BGR sang RGB để hiển thị đúng màu trong matplotlib.
axis('off'): Ẩn trục tọa độ để dễ nhìn.
tight_layout(): Canh chỉnh cho ảnh không bị chồng nhau.
show(): Hiển thị ảnh.

Bài 2

img = cv2.imread('exercise/colorful-ripe-tropical-fruits.jpg')
if img is None:
    print("❌ Không đọc được ảnh.")
    exit()

cv2.imread(...): Đọc ảnh từ đường dẫn.
Nếu ảnh không tồn tại hoặc lỗi khi đọc (img is None), in ra thông báo và thoát chương trình để tránh lỗi tiếp theo.

# Đu đủ
x1, y1, w1, h1 = 300, 450, 200, 210
# Dưa hấu
x2, y2, w2, h2 = 1800, 400, 600, 400

x, y: là tọa độ góc trên bên trái của vùng chọn.
w, h: là chiều rộng và chiều cao của vùng.
Vùng (x1, y1, w1, h1) là phần chứa quả đu đủ.
Vùng (x2, y2, w2, h2) là phần chứa dưa hấu.

img_edit = img.copy()

Tạo một bản sao ảnh (img_edit) để không thay đổi ảnh gốc.
Mọi chỉnh sửa sẽ được thực hiện trên img_edit.


# Đổi màu đu đủ thành xanh lá
img_edit[y1:y1+h1, x1:x1+w1] = [0, 255, 0]  # BGR
# Đổi màu dưa hấu thành tím
img_edit[y2:y2+h2, x2:x2+w2] = [255, 0, 255]  # BGR

img[y:y+h, x:x+w]: chọn vùng ảnh bằng chỉ số dòng và cột.
[0, 255, 0]: màu xanh lá cây trong hệ màu BGR (Blue, Green, Red).
[255, 0, 255]: màu tím (Red + Blue).
Hai dòng này giúp đổi toàn bộ pixel trong vùng tương ứng sang màu mới.

fig, axs = plt.subplots(1, 2, figsize=(12, 6))
axs[0].imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
axs[0].set_title('Ảnh gốc')
axs[0].axis('off')
axs[1].imshow(cv2.cvtColor(img_edit, cv2.COLOR_BGR2RGB))
axs[1].set_title('Sau khi đổi màu đu đủ và dưa hấu')
axs[1].axis('off')
plt.tight_layout()
plt.show()

plt.subplots(1, 2): Tạo 1 hàng, 2 cột để hiển thị 2 ảnh cạnh nhau.
cv2.cvtColor(..., cv2.COLOR_BGR2RGB): Đổi hệ màu từ BGR (OpenCV mặc định) sang RGB (matplotlib dùng).
axis('off'): Tắt hiển thị trục tọa độ cho đẹp.
tight_layout(): Canh lề cho các ảnh không bị chồng lên nhau.
show(): Hiển thị cửa sổ ảnh.

Bài 3

image_path = 'exercise/colorful-ripe-tropical-fruits.jpg'
save_path = 'exercise/fruits_rotated.jpg'
os.makedirs('exercise', exist_ok=True)

image_path: Đường dẫn ảnh gốc.
save_path: Nơi lưu ảnh kết quả.
os.makedirs(..., exist_ok=True): Tạo thư mục exercise nếu chưa có.

img = cv2.imread(image_path)
if img is None:
    print(f"❌ Không tìm thấy ảnh tại: {image_path}")
    exit()

Đọc ảnh từ image_path.
Nếu không tìm thấy ảnh, in lỗi và dừng chương trình.


clone = img.copy()
height, width = img.shape[:2]

clone: bản sao để giữ nguyên ảnh gốc.
height, width: kích thước ảnh (dòng và cột).

roi_width = 200
roi_height = 200
rois = []
for _ in range(2):
    x = random.randint(0, width - roi_width)
    y = random.randint(0, height - roi_height)
    rois.append((x, y, roi_width, roi_height))

roi_width, roi_height: kích thước của vùng chọn (ROI - Region of Interest).
Vòng lặp chọn 2 tọa độ ngẫu nhiên (x, y) sao cho vùng chọn không vượt quá ảnh.
Mỗi vùng là một tuple (x, y, w, h).

result = clone.copy()
for idx, (x, y, w, h) in enumerate(rois):
    roi = clone[y:y+h, x:x+w]
    center = (w // 2, h // 2)
    M = cv2.getRotationMatrix2D(center, 45, 1.0)
    rotated = cv2.warpAffine(roi, M, (w, h), borderValue=(0, 0, 0))
    result[y:y+h, x:x+w] = rotated
    cv2.imwrite(f"exercise/rotated_random_roi_{idx+1}.jpg", rotated)

clone.copy(): tạo ảnh kết quả từ bản sao.
Với từng vùng:
roi = ...: cắt ra vùng nhỏ từ ảnh.
cv2.getRotationMatrix2D(...): tạo ma trận xoay 45 độ quanh tâm vùng.
cv2.warpAffine(...): áp dụng phép xoay để tạo ảnh mới (rotated).
borderValue=(0, 0, 0): viền đen nếu xoay bị lộ góc.
result[y:y+h, x:x+w] = rotated: dán ảnh xoay ngược lại vào đúng vị trí.
cv2.imwrite(...): lưu từng vùng xoay riêng (tuỳ chọn).

cv2.imwrite(save_path, result)
print(f"✅ Đã lưu ảnh sau khi xoay 2 vùng ngẫu nhiên tại: {save_path}")

Lưu toàn bộ ảnh result vào save_path.

img_rgb = cv2.cvtColor(result, cv2.COLOR_BGR2RGB)
plt.imshow(img_rgb)
plt.title("Ảnh sau khi xoay 2 vùng ngẫu nhiên")
plt.axis("off")
plt.show()

Chuyển ảnh từ BGR (OpenCV mặc định) sang RGB để matplotlib hiển thị đúng màu.
Hiển thị ảnh kết quả đã xoay 2 vùng ngẫu nhiên.

Bài 4

image_path = 'exercise/colorful-ripe-tropical-fruits.jpg'
output_path = 'exercise/fruit_scaled.jpg'
os.makedirs('exercise', exist_ok=True)

image_path: đường dẫn ảnh gốc (ảnh trái cây).
output_path: đường dẫn nơi sẽ lưu ảnh đã phóng to.
os.makedirs(..., exist_ok=True): tạo thư mục exercise nếu chưa có để tránh lỗi lưu file.

img = cv2.imread(image_path)
if img is None:
    print("❌ Không tìm thấy ảnh tại:", image_path)
    exit()

cv2.imread(...): đọc ảnh từ file.
Nếu không tìm thấy ảnh (img is None), thì in ra thông báo lỗi và thoát chương trình.

x, y, w, h = 300, 450, 200, 200  # Bạn có thể điều chỉnh toạ độ này
Đây là tọa độ vùng ảnh muốn lấy ra để phóng to:
(x, y): góc trên bên trái của vùng.
w, h: chiều rộng và chiều cao (vùng cắt là hình vuông 200x200 pixel).
Trong ví dụ là vùng chứa quả đu đủ.

fruit_roi = img[y:y+h, x:x+w]

Dùng slicing để cắt ra một phần nhỏ từ ảnh gốc (vùng trái cây cần phóng to).
fruit_roi: lưu phần ảnh đã cắt.

fruit_scaled = cv2.resize(fruit_roi, None, fx=5, fy=5, interpolation=cv2.INTER_LINEAR)

cv2.resize(...): dùng để thay đổi kích thước ảnh.
fx=5, fy=5: phóng to theo cả chiều ngang và dọc gấp 5 lần.
interpolation=cv2.INTER_LINEAR: phương pháp nội suy tuyến tính, làm mịn ảnh khi phóng to.

cv2.imwrite(output_path, fruit_scaled)
print(f"✅ Đã lưu ảnh trái cây được phóng to tại: {output_path}")

cv2.imwrite(...): ghi ảnh đã phóng to ra file.
In ra thông báo xác nhận khi lưu xong.

img_rgb = cv2.cvtColor(fruit_scaled, cv2.COLOR_BGR2RGB)
plt.figure(figsize=(6, 6))
plt.imshow(img_rgb)
plt.axis("off")
plt.figtext(0.5, 0.01, "Ảnh sau khi phóng to vùng trái cây (5 lần)", 
            wrap=True, horizontalalignment='center', fontsize=12)
plt.show()

cv2.cvtColor(...): chuyển ảnh từ hệ màu BGR (OpenCV dùng) sang RGB (matplotlib yêu cầu).
plt.imshow(...): hiển thị ảnh.
axis("off"): ẩn trục tọa độ.
figtext(...): thêm chú thích phía dưới ảnh, chính giữa, để ghi rõ ý nghĩa ảnh.
show(): hiện ảnh trong cửa sổ đồ họa.

Bài 5

image_files = [
    'exercise/fruit_scaled.jpg',
    'exercise/kiwi_translated.jpg',
    'exercise/pagoda.jpg'
]
image_files = [img for img in image_files if os.path.exists(img)]
if not image_files:
    print("❌ Không có ảnh nào hợp lệ trong thư mục 'exercise'.")
    exit()

Danh sách 3 ảnh mẫu để chọn.
Lọc ra những ảnh nào thực sự tồn tại trong thư mục.
Nếu không có ảnh nào, in cảnh báo và thoát chương trình.

transformations = {
    'T': ('Tịnh tiến', lambda img, h, w: cv2.warpAffine(img, np.float32([[1, 0, 100], [0, 1, 50]]), (w, h))),
    'X': ('Xoay', lambda img, h, w: cv2.warpAffine(img, cv2.getRotationMatrix2D((w // 2, h // 2), 45, 1.0), (w, h))),
    'P': ('Phóng to', lambda img, h, w: cv2.resize(img, None, fx=1.5, fy=1.5, interpolation=cv2.INTER_LINEAR)),
    'H': ('Thu nhỏ', lambda img, h, w: cv2.resize(img, None, fx=0.5, fy=0.5, interpolation=cv2.INTER_AREA)),
    'C': ('Lật ngang', lambda img, h, w: cv2.flip(img, 1))
}

Tạo dictionary transformations:
Key là ký tự người dùng nhập (T, X, P, H, C).
Value là tên phép biến đổi + hàm thực hiện biến đổi tương ứng.

print("Chọn phép biến đổi:")
for key, (name, _) in transformations.items():
    print(f"{key} - {name}")
transform_key = input("Nhập ký tự tương ứng với phép biến đổi (T/X/P/H/C): ").strip().upper()

Hiển thị menu các lựa chọn.
Nhận đầu vào từ người dùng để biết họ chọn biến đổi nào.

if transform_key not in transformations:
    print("❌ Phép biến đổi không hợp lệ.")
    exit()

Nếu nhập sai ký tự, in lỗi và thoát chương trình.

print("\nChọn ảnh để áp dụng biến đổi:")
for idx, path in enumerate(image_files):
    print(f"{idx + 1} - {os.path.basename(path)}")

Hiển thị danh sách các ảnh có thể chọn.

try:
    img_index = int(input("Nhập số thứ tự ảnh (1/2/3): ")) - 1
    if img_index < 0 or img_index >= len(image_files):
        raise ValueError
except ValueError:
    print("❌ Lựa chọn ảnh không hợp lệ.")
    exit()

Nhận đầu vào (chọn ảnh số mấy).
Kiểm tra nếu chọn sai thì thoát chương trình.

img_path = image_files[img_index]
img = cv2.imread(img_path)
if img is None:
    print(f"❌ Không đọc được ảnh: {img_path}")
    exit()

h, w = img.shape[:2]
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

Đọc ảnh đã chọn.
Lấy chiều cao (h) và chiều rộng (w).
Chuyển ảnh sang RGB để hiển thị đúng màu bằng matplotlib.

name, transform_fn = transformations[transform_key]
transformed = transform_fn(img, h, w)

Lấy ra tên và hàm xử lý tương ứng với lựa chọn của người dùng.
Gọi hàm biến đổi ảnh (transform_fn(...)).

result_rgb = cv2.cvtColor(transformed, cv2.COLOR_BGR2RGB)
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.imshow(img_rgb)
plt.title("Ảnh gốc")
plt.axis("off")
plt.subplot(1, 2, 2)
plt.imshow(result_rgb)
plt.title(f"Biến đổi: {name}")
plt.axis("off")
filename = os.path.splitext(os.path.basename(img_path))[0]
plt.figtext(0.5, 0.01, f"{name} - {filename}", ha='center', fontsize=10)
plt.tight_layout()
plt.show()

Hiển thị ảnh gốc và ảnh sau biến đổi cạnh nhau.
Thêm chú thích tên phép biến đổi và tên ảnh bên dưới.

os.makedirs("exercise/results", exist_ok=True)
out_name = f"exercise/results/{filename}_{transform_key}.jpg"
cv2.imwrite(out_name, transformed)
print(f"✅ Đã lưu: {out_name}")

Tạo thư mục results nếu chưa có.
Lưu ảnh kết quả biến đổi theo định dạng: tên_ảnh_ký_tự_biến_đổi.jpg.


except Exception as e:
    print(f"❌ Lỗi khi thực hiện biến đổi: {e}")


Nếu có lỗi trong quá trình biến đổi (hiếm gặp), thông báo lỗi sẽ được in ra.
