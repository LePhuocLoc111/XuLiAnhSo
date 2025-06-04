Dưới đây là phần giải thích lại **README.md** cho phù hợp với code mới (có hiển thị ảnh bằng matplotlib trong Jupyter Notebook):

---

## Bài 1  
- **Nhập thư viện:**  
  `import numpy as np`, `import os`, `import cv2`, `import matplotlib.pyplot as plt`  
- **Tạo thư mục lưu ảnh:**  
  `os.makedirs('output_images', exist_ok=True)`  
- **Đọc ảnh:**  
  `img = cv2.imread('bird.png')`  
- **Tách kênh màu:**  
  `b, g, r = cv2.split(img)`  
- **Tạo ảnh chỉ giữ 1 kênh màu:**  
  - Đặt 2 kênh còn lại bằng 0, ghép lại bằng `cv2.merge`
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để hiển thị ảnh gốc và từng kênh màu ngay trên notebook.

---

## Bài 2  
- **Hoán đổi các kênh màu:**  
  - Đọc ảnh, dùng indexing để đổi chỗ các kênh màu (R-G, G-B, B-R)
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để xem trực tiếp ảnh đã đổi kênh màu.

---

## Bài 3  
- **Chuyển đổi sang HSV:**  
  `hsv_img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)`
- **Tách kênh HSV:**  
  `h, s, v = cv2.split(hsv_img)`
- **Tạo ảnh chỉ giữ 1 kênh:**  
  - Đặt các kênh còn lại ở mức tối đa để dễ quan sát.
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để xem từng kênh HSV.

---

## Bài 4  
- **Chỉnh sửa kênh Hue và Value:**  
  - Chia nhỏ giá trị Hue, giảm Value để thay đổi màu và độ sáng.
- **Gộp lại và chuyển về BGR:**  
  `result_img = cv2.cvtColor(hsv_modified, cv2.COLOR_HSV2BGR)`
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để so sánh ảnh gốc và ảnh đã chỉnh HSV.

---

## Bài 5  
- **Lọc trung bình (Mean filter):**  
  `mean_filtered = cv2.blur(img, (5, 5))`
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để xem ảnh gốc và ảnh đã lọc.

---

## Bài 6  
- **Lọc nhiễu bằng 3 kỹ thuật:**  
  - `cv2.blur(...)` (Mean filter)  
  - `cv2.GaussianBlur(...)` (Gaussian filter)  
  - `cv2.medianBlur(...)` (Median filter)
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để so sánh trực tiếp 4 ảnh: gốc, mean, gaussian, median.

---

## Bài 7  
- **Tìm biên bằng 3 phương pháp:**  
  - Chuyển ảnh về xám: `cv2.cvtColor(denoised, cv2.COLOR_BGR2GRAY)`
  - Canny: `cv2.Canny(...)`
  - Sobel: `cv2.Sobel(...)`
  - Laplacian: `cv2.Laplacian(...)`
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để xem ảnh gốc và các kết quả tìm biên.

---

## Bài 8  
- **Xáo trộn kênh màu RGB:**  
  - Tách kênh, xáo trộn thứ tự, ghép lại bằng `cv2.merge`
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để xem ảnh gốc và ảnh đã xáo trộn màu.

---

## Bài 9  
- **Xáo trộn kênh HSV:**  
  - Chuyển sang HSV, tách kênh, xáo trộn, ghép lại, chuyển về BGR.
- **Lưu ảnh:**  
  `cv2.imwrite(...)`
- **Hiển thị ảnh:**  
  Dùng `plt.imshow(...)` để xem ảnh gốc và ảnh đã xáo trộn HSV.

