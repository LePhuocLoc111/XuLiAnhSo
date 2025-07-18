Bài 1

Đọc ảnh

img = cv2.imread('a.jpg')
→ Đọc ảnh đầu vào từ file a.jpg.

Mean Filter (Làm mờ trung bình)

mean_filtered = cv2.blur(img, (5, 5))
→ Làm mờ ảnh bằng trung bình cộng trong cửa sổ 5x5 để giảm nhiễu.

Edge Detection (Phát hiện biên)

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
edges = cv2.Canny(gray, 100, 200)
→ Chuyển ảnh sang xám, sau đó phát hiện biên bằng thuật toán Canny.

Đổi màu RGB ngẫu nhiên

img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
random.shuffle([0, 1, 2])
random_color = img_rgb[:, :, [0, 2, 1]]  # ví dụ tráo thứ tự kênh
→ Tráo ngẫu nhiên thứ tự các kênh màu RGB → tạo màu sắc mới lạ.

Tách kênh HSV

hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
h, s, v = cv2.split(hsv)
→ Chuyển ảnh sang không gian màu HSV và tách 3 kênh:
H (Hue - màu sắc)
S (Saturation - độ bão hòa)
V (Value - độ sáng)

Hiển thị ảnh

plt.show()
→ Hiển thị tất cả ảnh đã xử lý bằng matplotlib.

Bài 2

Đọc 3 ảnh grayscale
image_files = ['image1.jpg', 'image2.jpg', 'image3.jpg']
cv2.imread(file, cv2.IMREAD_GRAYSCALE)

→ Đọc 3 ảnh ở chế độ ảnh xám, lưu vào danh sách images.

Hàm hiển thị
show_images()

→ Dùng matplotlib để hiển thị 3 ảnh đã xử lý cùng lúc với tiêu đề tương ứng.

Các hàm biến đổi ảnh

 Biến đổi                           | Giải thích                                            

image_inverse                    Đảo ngược màu: 255 - pixe`                          
gamma_correction(img, gamma)     Làm sáng/tối ảnh tùy vào gamma (0.5–2.0)              
log_transform(img, c)            Nén động sáng → làm rõ chi tiết tối                   
hist_equalization                Cân bằng histogram ảnh xám                            
contrast_stretching(img, r1, r2) Kéo giãn độ tương phản từ khoảng \[r1–r2] ra \[0–255] 
adaptive_hist_equalization`      Cân bằng cục bộ theo ô lưới 8×8 (CLAHE)               

Map phím → xử lý ảnh

operations = {
    'i': ('Inverse', image_inverse),
    'g': ('Gamma', lambda img: gamma_correction(img, random.uniform(0.5, 2.0))),
    ...
}

→ Mỗi phím tương ứng một tên biến đổi + hàm thực hiện (nhiều cái dùng random).

In menu + vòng lặp lựa chọn

while True:
    key = input("Nhập phím: ").lower()

→ Lặp vô hạn cho đến khi người dùng bấm Q.
→ Với mỗi lựa chọn:
Áp dụng biến đổi cho tất cả 3 ảnh
Lưu ảnh với tên theo định dạng:
output_[tên biến đổi]_[số ảnh].jpg
Hiển thị ảnh sau biến đổi

Bài 3
Hàm phụ trợ hiển thị ảnh
def show(title, img):
    img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    plt.imshow(img_rgb)
    plt.title(title)
    plt.axis('off')
    plt.show()
→ Chuyển ảnh từ BGR sang RGB để hiển thị đúng màu bằng matplotlib.

1. Tăng kích thước ảnh trái cây
cv2.resize(img_fruit, (width + 30, height + 30))

Đọc ảnh colorful-ripe-tropical-fruits.jpg
Tăng cả chiều ngang và dọc thêm 30 pixels
Lưu ra resized_fruit.jpg

2. Xoay và lật ảnh Quảng Ninh
M = cv2.getRotationMatrix2D(center, -45, 1.0)
rotated = cv2.warpAffine(img_qn, M, (w, h))
flipped = cv2.flip(rotated, 1)

Xoay ảnh quang_ninh.jpg 45 độ ngược chiều kim đồng hồ
Sau đó lật ngang ảnh (flip code = 1)
Lưu kết quả thành rotated_flipped_qn.jpg

3. Phóng to và làm mịn ảnh chùa
resized_pagoda = cv2.resize(img_pagoda, (0, 0), fx=5, fy=5)
blurred = cv2.GaussianBlur(resized_pagoda, (7, 7), 0)

Phóng to ảnh pagoda.jpg lên gấp 5 lần
Làm mờ bằng Gaussian Blur với kernel 7×7
Lưu thành blurred_pagoda.jpg

 4. Điều chỉnh độ sáng & tương phản ảnh
adjusted = alpha * resized_pagoda + beta

Áp dụng công thức tuyến tính:
I_out = α * I_in + β
α ∈ [0.5, 2.0]: thay đổi độ tương phản
β ∈ [-50, 50]: thay đổi độ sáng
 Dùng np.clip(..., 0, 255) để giữ ảnh trong dải hợp lệ
 Lưu kết quả thành adjusted_pagoda.jpg



