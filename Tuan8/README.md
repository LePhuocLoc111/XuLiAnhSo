Import thư viện


from PIL import Image
import numpy as np
import imageio.v2 as iio
import scipy.ndimage as nd
from skimage.morphology import label
from skimage.measure import regionprops
import matplotlib.pylab as plt
import matplotlib.patches as mpatches
from skimage.filters.thresholding import threshold_otsu

Dòng này nạp các thư viện cần thiết cho việc:
Xử lý ảnh (PIL, numpy, imageio, scipy, skimage)
Tìm vùng đối tượng (label, regionprops)
Vẽ biểu đồ (matplotlib)
Áp dụng ngưỡng tự động (threshold_otsu)

Bài 1

data = Image.open('geometric.png').convert('L')
a = np.asarray(data)

Mở ảnh 'geometric.png' và chuyển về ảnh xám (grayscale).
a là ma trận numpy chứa cường độ sáng (0–255) của ảnh.

thres = threshold_otsu(a)
b = a > thres

Dùng phương pháp Otsu để tính giá trị ngưỡng thres.
Ảnh nhị phân b sẽ là:
True (1) nếu pixel > ngưỡng ⇒ đối tượng
False (0) nếu pixel <= ngưỡng ⇒ nền

c = label(b)
cl = Image.fromarray(c)
iio.imsave('label_output.jpg', cl)

label(b) gán số (1, 2, 3...) cho từng vùng trắng (vật thể) riêng biệt trong ảnh nhị phân.
Lưu kết quả ra ảnh 'label_output.jpg'.

properties = ['Area', 'Centroid', 'BoundingBox']
d = regionprops(c)

Hàm regionprops lấy các thông tin hình học của từng vùng:
Area: diện tích
Centroid: tâm đối tượng
BoundingBox: khung chữ nhật bao quanh

fig, ax = plt.subplots(ncols=1, nrows=1, figsize=(6, 6))
ax.imshow(c, cmap='YlOrRd')

Tạo khung hiển thị ảnh với màu sắc dễ phân biệt (YlOrRd).
figsize=(6,6) nghĩa là tạo hình kích thước 6 inch × 6 inch.

for i in d:
    lr, lc, ur, uc = i['BoundingBox']
    rec_width = uc - lc
    rec_height = ur - lr

    rect = mpatches.Rectangle((lc, lr), rec_width, rec_height, fill=False,
                              edgecolor='black', linewidth=2)
    ax.add_patch(rect)

Với mỗi vùng đối tượng:
Lấy tọa độ khung bao (BoundingBox)
Tính chiều dài và rộng
Vẽ khung chữ nhật không tô màu, viền đen, nét dày 2px

plt.show()

Hiển thị ảnh đã dán nhãn và vẽ khung lên từng vùng.

Bài 2

data = Image.open('geometric.png').convert('L')

Đọc ảnh "geometric.png".
.convert('L'): chuyển ảnh thành ảnh đen trắng (ảnh xám, mỗi pixel chỉ có 1 giá trị sáng từ 0–255).

bmg = abs(data - nd.shift(data, (0, 1), order=0))

nd.shift(data, (0, 1), order=0): dịch ảnh sang phải 1 pixel (0 hàng, 1 cột).
data - ...: lấy hiệu giữa ảnh gốc và ảnh bị dịch → phát hiện vùng khác biệt (tức là biên, cạnh).
abs(...): lấy trị tuyệt đối để tránh âm (vì ảnh không thể có giá trị âm).

plt.imshow(bmg)
plt.show()

plt.imshow(bmg): hiển thị ảnh chứa các đường biên vừa phát hiện.
plt.show(): vẽ ảnh ra cửa sổ hiển thị.

Bài 3

data = Image.open('geometric.png')

Đọc ảnh từ file "geometric.png" bằng thư viện PIL.
Ảnh này có thể là ảnh RGB hoặc Grayscale.

a = nd.sobel(data, axis=0)  # Dọc
b = nd.sobel(data, axis=1)  # Ngang

sobel(data, axis=0): phát hiện biên theo chiều dọc (dọc cột).
sobel(data, axis=1): phát hiện biên theo chiều ngang (ngang hàng).

bmg = abs(a) + abs(b)

Lấy trị tuyệt đối của hai kết quả Sobel theo chiều x và y.
Cộng lại để được ảnh biểu diễn biên tổng hợp (gradient magnitude).

plt.imshow(bmg)
plt.show()

imshow(bmg): hiển thị ảnh biên bmg.
plt.show(): vẽ ảnh ra cửa sổ hiển thị

Bài 3 

def Harris(indata, alpha=0.2):

indata: ảnh đầu vào (ảnh mức xám).
alpha: hệ số nhạy cảm (thường dùng giữa 0.04 và 0.2, ở đây là 0.2).

    x = nd.sobel(indata, 0)  # Gradient theo trục x
    y = nd.sobel(indata, 1)  # Gradient theo trục y

Sử dụng bộ lọc Sobel để tính đạo hàm của ảnh theo hai hướng:
x: theo chiều ngang.
y: theo chiều dọc.

    xl = x ** 2           # Ix^2
    yl = y ** 2           # Iy^2
    xy = abs(x * y)       # Ix * Iy

Đây là các thành phần được dùng trong ma trận tự cộng tích (structure tensor), để tìm ra vùng có thay đổi mạnh về cường độ (ví dụ: góc).

    xl = nd.gaussian_filter(xl, 3)
    yl = nd.gaussian_filter(yl, 3)
    xy = nd.gaussian_filter(xy, 3)

Làm mượt các giá trị đạo hàm bằng bộ lọc Gaussian để giảm nhiễu, giúp xác định điểm góc rõ ràng hơn.

    detC = xl * yl - 2 * xy
    trC = xl + yl

detC (determinant): xác định độ “căng” của ảnh ở 1 điểm.
trC (trace): tổng mức thay đổi cường độ.
Công thức Harris response (điểm có giá trị cao là góc):

data = Image.open('geometric.png').convert('L')

Đọc ảnh "geometric.png" và chuyển sang grayscale (nếu chưa).

bmg = Harris(data)

Áp dụng hàm Harris lên ảnh vừa đọc được.

plt.imshow(bmg)
plt.show()

Hiển thị ma trận R (phản ứng Harris).
Những vùng có giá trị cao là nơi có khả năng là góc hoặc vùng biên rõ.

Bài 4

def LineHough(data, gamma):

data: ảnh nhị phân đầu vào (đã có các điểm biên hoặc điểm sáng).
gamma: ngưỡng để dừng vòng lặp nếu giá trị lớn nhất trong ảnh nhỏ hơn gamma.

V, H = data.shape
R = int(np.sqrt(V * V + H * H))   # Bán kính tối đa trong hệ tọa độ Hough
ho = np.zeros((R, 90), float)     # Tạo không gian Hough (r, θ)

R: độ dài tối đa từ gốc đến bất kỳ điểm nào trong ảnh (đường chéo).
ho: ma trận Hough accumulator (tích lũy phiếu bầu cho các đường thẳng).

w = data + 0       # sao chép mảng ảnh
ok = 1             # cờ điều khiển vòng lặp
theta = np.arange(90) / 180.0 * np.pi  # danh sách góc từ 0 đến 89 độ (radian)
tp = np.arange(90).astype(float)       # góc dạng float để truy cập chỉ số

while ok:
    mx = w.max()
    if mx < gamma:
        ok = 0
    else:
        v, h = divmod(w.argmax(), H)  # tọa độ pixel có giá trị sáng nhất
        y = V - v
        x = h

Ý tưởng: tìm điểm sáng nhất còn lại trên ảnh w.
Nếu không còn điểm nào sáng hơn gamma, thì dừng.

        rh = x * np.cos(theta) + y * np.sin(theta)

Dựa vào công thức Hough:
                
ρ=x⋅cos(θ)+y⋅sin(θ)

for i in range(len(rh)):
            if 0 <= rh[i] < R and 0 <= tp[i] < 90:
                ho[int(rh[i]), int(tp[i])] += mx
        w[v, h] = 0

Với mỗi θ, cộng giá trị sáng mx vào ô [r, θ] tương ứng trong không gian Hough.
Sau đó gán giá trị điểm đã dùng về 0 để không xét lại.

    return ho

Trả về không gian Hough: chứa tích lũy phiếu bầu cho các đường thẳng.

data = np.zeros((256, 256))
data[128, 128] = 1

Tạo ảnh nền đen 256×256 với 1 điểm trắng ở giữa → để kiểm tra thuật toán đơn giản.

bmg = LineHough(data, 0.5)
plt.imshow(bmg)
plt.show()

Áp dụng Hough transform lên ảnh.
Hiển thị Hough space, nơi các điểm sáng là những đường thẳng được phát hiện.

Bài 5

data = iio.imread('bird.png')  # Đọc ảnh màu từ file
image_gray = rgb2gray(data)    # Chuyển sang ảnh xám [0,1]

iio.imread() đọc ảnh dưới dạng NumPy array.
rgb2gray() từ skimage.color chuyển ảnh màu thành ảnh xám, chuẩn hóa giá trị pixel về [0, 1].

coordinate = corner_harris(image_gray, k=0.001)

corner_harris() là hàm từ skimage.feature để phát hiện các điểm góc trong ảnh xám.
k=0.001: hệ số nhạy cảm với "độ cong" của góc. Thường nằm trong khoảng 0.04 đến 0.06. Bạn dùng giá trị nhỏ để tăng độ nhạy.

plt.figure(figsize=(20,10))
plt.imshow(coordinate)
plt.axis('off')
plt.show()

Vẽ kết quả dưới dạng bản đồ nhiệt (imshow()).
plt.axis('off'): ẩn trục để nhìn ảnh gọn hơn.
figsize=(20,10): kích thước ảnh lớn giúp dễ quan sát.

Bài 6

img1 = cv2.imread('bird.png')
img2 = cv2.imread('geometric.png')
gray1 = cv2.cvtColor(img1, cv2.COLOR_BGR2GRAY)
gray2 = cv2.cvtColor(img2, cv2.COLOR_BGR2GRAY)

cv2.imread() đọc hai ảnh màu.
cv2.cvtColor(..., cv2.COLOR_BGR2GRAY) chuyển ảnh sang ảnh xám, cần thiết cho các thuật toán như Harris hay ORB.

corners1 = cv2.cornerHarris(np.float32(gray1), 2, 3, 0.04)
corners2 = cv2.cornerHarris(np.float32(gray2), 2, 3, 0.04)

cornerHarris(...) tìm các điểm góc (corner) trong ảnh.
Tham số:
2: kích thước khối (block size) dùng để tính gradient.
3: kích thước khẩu độ của Sobel.
0.04: hệ số Harris k.
Tuy nhiên, kết quả corners1 và corners2 không được sử dụng tiếp, vì ORB sẽ tự phát hiện keypoints.

orb = cv2.ORB_create()
kp1, des1 = orb.detectAndCompute(gray1, None)
kp2, des2 = orb.detectAndCompute(gray2, None)

Tạo đối tượng ORB (Oriented FAST and Rotated BRIEF).
detectAndCompute():
kp1, kp2: các điểm đặc trưng (keypoints).
des1, des2: vector mô tả đặc trưng (descriptors), dùng để so khớp.

bf = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)
matches = bf.match(des1, des2)
matches = sorted(matches, key=lambda x: x.distance)

BFMatcher: Bộ so khớp Brute-Force dùng khoảng cách Hamming (do ORB tạo ra nhị phân).
crossCheck=True: đảm bảo mỗi điểm ở ảnh 1 chỉ khớp với 1 điểm ở ảnh 2 (và ngược lại).
matches được sắp xếp theo distance tăng dần → các cặp điểm giống nhau nhất đứng đầu.

matched_img = cv2.drawMatches(img1, kp1, img2, kp2, matches[:20], None, flags=2)

Vẽ 20 cặp điểm có khoảng cách nhỏ nhất (tức là giống nhau nhất).
flags=2 để vẽ đường nối và keypoints đơn giản.

plt.figure(figsize=(14, 6))
plt.imshow(cv2.cvtColor(matched_img, cv2.COLOR_BGR2RGB))
plt.title("So khớp đặc trưng giữa hai ảnh")
plt.axis('off')
plt.show()

Dùng matplotlib để hiển thị ảnh đã vẽ đường so khớp.
cv2.cvtColor(..., COLOR_BGR2RGB) chuyển màu từ BGR (OpenCV mặc định) sang RGB để hiển thị đúng trên matplotlib.

