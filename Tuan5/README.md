Bài 1
import numpy as np
import imageio.v2 as iio
import matplotlib.pylab as plt

data = iio.imread('fruit.jpg')  # Đọc ảnh gốc, trả về mảng numpy 3 chiều (cao, rộng, màu)
bmg = data[800:1200, 570:980]   # Cắt vùng ảnh: từ dòng 800 đến 1199, cột 570 đến 979
print(data.shape)               # In kích thước ảnh gốc (cao, rộng, số kênh màu)
iio.imsave('orange.jpg', bmg)   # Lưu vùng ảnh vừa cắt ra file mới
plt.imshow(bmg)                 # Hiển thị vùng ảnh vừa cắt
plt.show()

Bài 2
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

data = iio.imread('fruit.jpg', pilmode='F')  # Đọc ảnh mức xám dạng float
bdata = nd.shift(data, (100, 25))            # Dịch chuyển ảnh: xuống 100 pixel, sang phải 25 pixel
plt.imshow(bdata, cmap='gray')               # Hiển thị ảnh đã dịch chuyển
plt.show()

Bài 3
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pylab as plt

data = iio.imread('fruit.jpg')           # Đọc ảnh gốc
print(data.shape)                        # In kích thước ảnh gốc

bdata = nd.zoom(data, 2)                 # Phóng to 2 lần cả chiều cao và rộng
print(bdata.shape)                       # In kích thước ảnh sau khi phóng to

data2 = nd.zoom(data, (2, 2, 1))         # Phóng to 2 lần chiều cao, rộng, giữ nguyên kênh màu
print(data2.shape)

data3 = nd.zoom(data, (0.5, 0.9, 1))     # Thu nhỏ 0.5 lần chiều cao, 0.9 lần chiều rộng
plt.imshow(data3)                        # Hiển thị ảnh đã thu nhỏ
plt.show()

Bài 4
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

data = iio.imread('fruit.jpg')           # Đọc ảnh gốc
print(data.shape)

d1 = nd.rotate(data, 20)                 # Xoay 20 độ, reshape=True (ảnh sẽ lớn hơn, không bị cắt góc)
plt.imshow(d1)
plt.show()

d2 = nd.rotate(data, 20, reshape=False)  # Xoay 20 độ, reshape=False (giữ nguyên kích thước, bị cắt góc)
plt.imshow(d2)
plt.show()

Bài 5
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

data = iio.imread('world_cup.jpg', mode='F')  # Đọc ảnh mức xám float
print(data.shape)

d1 = nd.binary_dilation(data)                 # Giãn nở 1 lần: vùng trắng lan rộng ra
plt.imshow(d1, cmap='gray')
plt.show()

d2 = nd.binary_dilation(data, iterations=3)   # Giãn nở 3 lần: vùng trắng lan rộng hơn nữa
plt.imshow(d2, cmap='gray')
plt.show()

Bài 6
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

data = iio.imread('world_cup.jpg', mode="F") # Đọc ảnh mức xám float
print(data.shape)

V, H = data.shape                            # Lấy kích thước ảnh
M = np.indices((V, H))                       # Tạo lưới chỉ số pixel
d = 5
q = 2 * d * np.random.rand(*M.shape) - d     # Sinh nhiễu ngẫu nhiên [-5, 5]
mp = (M + q).astype(int)                     # Cộng nhiễu vào chỉ số pixel
d1 = nd.map_coordinates(data, mp)            # Lấy giá trị pixel theo lưới mới (ảnh bị méo)
plt.imshow(d1, cmap='gray')
plt.show()

Bài 7
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

def GeoFun(outcoord):
    a = 10 * np.cos(outcoord[0] / 10.0) + outcoord[0]  # Biến đổi theo cosin trục dọc
    b = 10 * np.cos(outcoord[1] / 10.0) + outcoord[1]  # Biến đổi theo cosin trục ngang
    return a, b

data = iio.imread('world_cup.jpg', pilmode="F")        # Đọc ảnh mức xám float
print(data.shape)

d1 = nd.geometric_transform(data, GeoFun)              # Áp dụng biến đổi hình học
plt.imshow(d1, cmap='gray')
plt.title("Ảnh sau khi áp dụng biến đổi hình học")
plt.axis('off')
plt.show()

Bài 8
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

data = iio.imread('fruit.jpg', pilmode='F')    # Đọc ảnh mức xám float
bdata = nd.shift(data, (-50, 400))             # Dịch chuyển lên 50, sang phải 400 pixel
plt.imshow(bdata, cmap='gray')
plt.title('Dịch chuyển để lấy đối tượng khác (quả táo)')
plt.axis('off')
plt.show()

Bài 9
import numpy as np
import scipy.ndimage as nd
import imageio.v2 as iio
import matplotlib.pyplot as plt

data = iio.imread('fruit.jpg', pilmode='F')    # Đọc ảnh mức xám float
bdata = nd.shift(data, (50, -30))              # Dịch chuyển xuống 50, sang trái 30 pixel
plt.imshow(bdata, cmap='gray')
plt.title('Ảnh sau khi tịnh tiến (50, -30)')
plt.axis('off')
plt.show()

Bài 10
import numpy as np
import imageio.v2 as iio
import matplotlib.pyplot as plt
from scipy.ndimage import zoom

data = iio.imread('fruit.jpg', pilmode='F')    # Đọc ảnh mức xám float
zoomed_up = zoom(data, 3.0)                    # Phóng to 3 lần
zoomed_down = zoom(data, 0.3)                  # Thu nhỏ 0.3 lần

fig, axs = plt.subplots(1, 3, figsize=(15, 5))
axs[0].imshow(data, cmap='gray')
axs[0].set_title('Ảnh gốc')
axs[0].axis('off')

axs[1].imshow(zoomed_up, cmap='gray')
axs[1].set_title('Phóng to x3')
axs[1].axis('off')

axs[2].imshow(zoomed_down, cmap='gray')
axs[2].set_title('Thu nhỏ x0.3')
axs[2].axis('off')

plt.tight_layout()
plt.show()

Giải thích từng bước:
Đọc ảnh:
iio.imread('fruit.jpg', pilmode='F') đọc ảnh "fruit.jpg" và chuyển sang ảnh mức xám kiểu float (giá trị pixel từ 0 đến 255).
Phóng to ảnh:
zoom(data, 3.0) phóng to cả chiều cao và chiều rộng lên 3 lần.
Ảnh sẽ lớn hơn, các chi tiết cũng được "kéo dãn" ra.
Thu nhỏ ảnh:
zoom(data, 0.3) thu nhỏ cả chiều cao và chiều rộng còn 0.3 lần so với ảnh gốc.
Ảnh sẽ nhỏ lại, các chi tiết bị nén lại.
Hiển thị:
Dùng plt.subplots để tạo 3 ô hiển thị: ảnh gốc, ảnh phóng to, ảnh thu nhỏ.
imshow(..., cmap='gray') để hiển thị ảnh mức xám.
axis('off') tắt trục tọa độ cho đẹp.

Bài 11

import numpy as np
import imageio.v2 as iio
import matplotlib.pyplot as plt
from scipy.ndimage import rotate

data = iio.imread('fruit.jpg', pilmode='F')    # Đọc ảnh mức xám float
rotated_true = rotate(data, 45, reshape=True)  # Xoay 45 độ, mở rộng kích thước
rotated_false = rotate(data, 45, reshape=False)# Xoay 45 độ, giữ nguyên kích thước

fig, axs = plt.subplots(1, 3, figsize=(15, 5))
axs[0].imshow(data, cmap='gray')
axs[0].set_title('Ảnh gốc')
axs[0].axis('off')

axs[1].imshow(rotated_true, cmap='gray')
axs[1].set_title('Xoay 45° (reshape=True)')
axs[1].axis('off')

axs[2].imshow(rotated_false, cmap='gray')
axs[2].set_title('Xoay 45° (reshape=False)')
axs[2].axis('off')

plt.tight_layout()
plt.show()

Giải thích từng bước:
Đọc ảnh:
Đọc ảnh mức xám kiểu float.
Xoay ảnh với reshape=True:
rotate(data, 45, reshape=True) xoay ảnh 45 độ, mở rộng kích thước ảnh để không bị mất góc.
Ảnh kết quả sẽ lớn hơn, không bị cắt góc, nhưng có thể xuất hiện viền đen ở các góc.
Xoay ảnh với reshape=False:
rotate(data, 45, reshape=False) xoay ảnh 45 độ, giữ nguyên kích thước ảnh gốc.
Các phần bị xoay ra ngoài sẽ bị cắt mất, ảnh không có viền đen nhưng có thể mất thông tin ở góc.
Hiển thị:
So sánh trực quan giữa ảnh gốc, ảnh xoay mở rộng, ảnh xoay giữ nguyên kích thước.

Bài 12
import numpy as np
import imageio.v2 as iio
import matplotlib.pyplot as plt
import scipy.ndimage as nd

data = iio.imread('world_cup.jpg', as_gray=True)      # Đọc ảnh mức xám
dilated = nd.grey_dilation(data, size=(5, 5))         # Giãn nở xám với kernel 5x5
eroded = nd.grey_erosion(data, size=(5, 5))           # Co xám với kernel 5x5

fig, axs = plt.subplots(1, 3, figsize=(15, 5))
axs[0].imshow(data, cmap='gray')
axs[0].set_title('Ảnh gốc')
axs[0].axis('off')

axs[1].imshow(dilated, cmap='gray')
axs[1].set_title('Dilation (5,5)')
axs[1].axis('off')

axs[2].imshow(eroded, cmap='gray')
axs[2].set_title('Erosion (5,5)')
axs[2].axis('off')

plt.tight_layout()
plt.show()

Giải thích từng bước:
Đọc ảnh:
Đọc ảnh mức xám (1 kênh), giá trị pixel từ 0 (đen) đến 255 (trắng).
Dilation (giãn nở):
grey_dilation(data, size=(5, 5)) lấy giá trị lớn nhất trong vùng 5x5 quanh mỗi pixel.
Vùng sáng (trắng) sẽ "nở" ra, các chi tiết nhỏ sáng sẽ lớn hơn.
Erosion (co lại):
grey_erosion(data, size=(5, 5)) lấy giá trị nhỏ nhất trong vùng 5x5 quanh mỗi pixel.
Vùng tối (đen) sẽ "nở" ra, vùng sáng bị "co" lại, các chi tiết nhỏ tối sẽ lớn hơn.
Hiển thị:
So sánh ảnh gốc, ảnh sau khi dilation, ảnh sau khi erosion.









