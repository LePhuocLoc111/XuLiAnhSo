Bài 1: 
Nhập thư viện:
from PIL import Image, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Đọc ảnh 'bird.png' và chuyển sang ảnh xám.
Chuyển ảnh sang mảng numpy:
im_1 = np.asarray(img)

Để dễ xử lý số học.
Tạo ảnh âm bản:
im_2 = 255 - im_1

Đảo ngược giá trị mức xám của từng pixel.
Chuyển về ảnh:
new_img = Image.fromarray(im_2)

Hiển thị ảnh:
img.show() (ảnh gốc), plt.imshow(new_img) (ảnh âm bản)

Bài 2: 
Nhập thư viện:
from PIL import Image, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy:
im_1 = np.asarray(img)

Chuyển sang float:
b1 = im_1.astype(float)

Chuẩn hóa về [0,1]:
norm_img = b1 / 255.0

Áp dụng gamma:
gamma = 0.5
gamma_corrected_norm = np.power(norm_img, gamma)

Đưa về [0,255]:
c = gamma_corrected_norm * 255.0
c = np.clip(c, 0, 255)

Chuyển về uint8:
c1 = c.astype(np.uint8)

Tạo ảnh và hiển thị:
d = Image.fromarray(c1)
plt.imshow(d, cmap='gray')

Bài 3: 
Nhập thư viện:
from PIL import Image, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy và float:
im_1 = np.asarray(img)
b1 = im_1.astype(float)

Tìm giá trị lớn nhất:
b2_max = np.max(b1)

Áp dụng biến đổi log:

if b2_max == 0: 
    c = np.zeros_like(b1) 
else: 
    c = (128.0 * np.log(1 + b1)) / np.log(1 + b2_max)

Đưa về [0,255] và uint8:
c = np.clip(c, 0, 255)
c1 = c.astype(np.uint8)

Tạo ảnh và hiển thị:
d = Image.fromarray(c1)
plt.imshow(d, cmap='gray')

Bài 4: 
Nhập thư viện:
from PIL import Image, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy:
iml = np.asarray(img)

Tính histogram và CDF:
bl = iml.flatten()
hist, bins = np.histogram(iml, 256, [0, 255])
cdf = hist.cumsum()
cdf_m = np.ma.masked_equal(cdf, 0)
num_cdf_m = (cdf_m - cdf_m.min()) * 255
den_cdf_m = (cdf_m.max() - cdf_m.min())
cdf_m = num_cdf_m / den_cdf_m
cdf = np.ma.filled(cdf_m, 0).astype('uint8')


Ánh xạ lại giá trị pixel:
im2 = cdf[bl]
im3 = np.reshape(im2, iml.shape)

Tạo ảnh và hiển thị:
im4 = Image.fromarray(im3)
img.show() (gốc), im4.show() (kết quả), plt.imshow(im4)

Bài 5:
Nhập thư viện:
from PIL import Image, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy:
iml = np.asarray(img)

Tìm min/max:
b = iml.max()
a = iml.min()

Chuyển sang float:
c = iml.astype(float)

Kéo dãn tương phản:
im2 = 255 * (c - a) / (b - a)

Tạo ảnh và hiển thị:
im3 = Image.fromarray(im2)
img.show() (gốc), im3.show() (kết quả), plt.imshow(im3)

Bài 6: 
Nhập thư viện:
from PIL import Image, import scipy, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy:
iml = np.asarray(img)

Biến đổi Fourier:
c = abs(scipy.fftpack.fft2(iml))

Dịch chuyển tần số:
d = scipy.fftpack.fftshift(c)

Chuyển về float và tạo ảnh:
d = d.astype(float)
im3 = Image.fromarray(d)

Hiển thị:
img.show() (gốc), im3.show() (phổ), plt.imshow(im3)

Bài 7: 
Nhập thư viện:
from PIL import Image, import math, import scipy, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy và FFT:
iml = np.asarray(img)
c = abs(scipy.fftpack.fft2(iml))
d = scipy.fftpack.fftshift(c)

Tạo mặt nạ Butterworth:
M, N = d.shape
H = np.ones((M, N))
center1 = M/2
center2 = N/2
d_0 = 30.0
t1 = 1
for i in range(1, M):
    for j in range(1, N):
        r = math.sqrt((i - center1)**2 + (j - center2)**2)
        if r > d_0:
            H[i, j] = 1 / (1 + (r / d_0)**t1)

Nhân phổ với mặt nạ, IFFT:
con = d * H
e = abs(scipy.fftpack.ifft2(con))
e = e.astype(float)
im3 = Image.fromarray(e)

Hiển thị:
img.show() (gốc), im3.show() (lọc thấp), plt.imshow(im3)

Bài 8:
Nhập thư viện:
from PIL import Image, import math, import scipy, import numpy as np, import matplotlib.pyplot as plt

Đọc ảnh:
img = Image.open('bird.png').convert('L')

Chuyển sang numpy và FFT:
iml = np.asarray(img)
c = abs(scipy.fftpack.fft2(iml))
d = scipy.fftpack.fftshift(c)

Tạo mặt nạ Butterworth thông cao:
M, N = d.shape
H = np.ones((M, N))
center1 = M/2
center2 = N/2
d_0 = 30.0
t1 = 2
t2 = 2 * t1
for i in range(1, M):
    for j in range(1, N):
        r = math.sqrt((i - center1)**2 + (j - center2)**2)
        if r > d_0:
            H[i, j] = 1 / (1 + (r / d_0)**t2)

Nhân phổ với mặt nạ, IFFT:
con = d * H
e = abs(scipy.fftpack.ifft2(con))
e = e.astype(float)
im3 = Image.fromarray(e)

Hiển thị:
img.show() (gốc), im3.show() (lọc cao), plt.imshow(im3)


