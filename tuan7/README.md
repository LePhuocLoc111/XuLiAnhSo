Import thư viện

from PIL import Image
import numpy as np
import imageio.v2 as iio
import scipy.ndimage as nd
import matplotlib.pylab as plt
from skimage.filters.thresholding import threshold_otsu

from PIL import Image: dùng thư viện PIL để mở và xử lý ảnh.
import numpy as np: dùng để xử lý ảnh dưới dạng mảng số.
import imageio.v2 as iio: thư viện đọc/ghi ảnh (trong đoạn mã này không sử dụng).
import scipy.ndimage as nd: xử lý ảnh nâng cao (cũng không sử dụng ở đây).
import matplotlib.pylab as plt: dùng để hiển thị ảnh bằng đồ họa.
from skimage.filters.thresholding import threshold_otsu: nhập hàm để tính ngưỡng Otsu từ ảnh.

Bài 1

data = Image.open('./exercise/dalat.jpg').convert('L')
a = np.asarray(data)

Image.open('./exercise/dalat.jpg'): mở ảnh từ thư mục exercise.
.convert('L'): chuyển ảnh sang mức xám (grayscale).
np.asarray(data): chuyển ảnh từ kiểu PIL sang mảng numpy, để thuận tiện xử lý số liệu ảnh.

thres = threshold_otsu(a)

Hàm threshold_otsu(a) tự động tính toán ngưỡng phân tách tối ưu giữa hai nhóm pixel:
Nhóm tối (nền)
Nhóm sáng (đối tượng)
Kết quả là một giá trị thres trong khoảng từ 0 đến 255.

b = a > thres

So sánh giá trị từng pixel trong ảnh a với ngưỡng thres.
Nếu pixel lớn hơn ngưỡng: giá trị trả về là True.
Nếu pixel nhỏ hơn hoặc bằng ngưỡng: trả về False.
Kết quả b là một mảng Boolean (True/False), còn gọi là ảnh nhị phân.

b = Image.fromarray(b)

Dùng Image.fromarray(b) để chuyển mảng b (kiểu Boolean) về ảnh dạng PIL.Image.
Mục đích là để có thể hiển thị được bằng matplotlib.

plt.imshow(b)
plt.show()

plt.imshow(b): hiển thị ảnh b đã xử lý.
plt.show(): hiển thị cửa sổ đồ họa.
Do không khai báo thêm cmap='gray', nên matplotlib sẽ dùng màu mặc định để hiển thị (có thể ra màu giả, không phải trắng đen).

Bài 2

data = Image.open('./exercise/dalat.jpg').convert('L')

Image.open(...): đọc ảnh dalat.jpg từ thư mục exercise.
.convert('L'): chuyển ảnh sang chế độ xám (grayscale), mỗi pixel có giá trị 0–255.

a = np.asarray(data)

Chuyển ảnh từ kiểu PIL.Image sang mảng NumPy, để dễ xử lý bằng toán học và các hàm xử lý ảnh.

b = threshold_local(a, block_size=39, offset=10)

threshold_local(...) tính ngưỡng riêng cho mỗi vùng nhỏ (ô vuông) trong ảnh, thay vì toàn ảnh.
block_size=39: mỗi vùng nhỏ là ô vuông 39x39 pixel.
offset=10: giảm ngưỡng mỗi vùng đi 10 đơn vị để điều chỉnh độ nhạy (giúp tăng độ tương phản giữa nền và vật thể).
Kết quả b là mảng ngưỡng cục bộ, có kích thước bằng ảnh gốc.

l = a > b

So sánh từng pixel với ngưỡng tương ứng tại cùng vị trí (vì b có kích thước giống a).
Nếu pixel sáng hơn ngưỡng tại vị trí đó → True (trắng).
Nếu tối hơn hoặc bằng ngưỡng → False (đen).
Kết quả là ảnh nhị phân (giá trị True/False).

plt.imshow(binary_local, cmap='gray')
plt.axis('off')
plt.show()

plt.imshow(...): hiển thị ảnh nhị phân binary_local.
cmap='gray': hiển thị theo đúng thang độ xám (đen-trắng).
plt.axis('off'): ẩn trục để ảnh đẹp hơn.
plt.show(): hiển thị ảnh ra màn hình.

Bài 3

data = cv2.imread('./exercise/dalat.jpg')
a = cv2.cvtColor(data, cv2.COLOR_BGR2GRAY)

cv2.imread(...): đọc ảnh gốc dưới dạng màu (BGR).
cv2.cvtColor(..., cv2.COLOR_BGR2GRAY): chuyển ảnh sang ảnh xám, để dễ xử lý nhị phân và phân vùng.

thresh, b1 = cv2.threshold(a, 0, 255, cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU)

cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU:
Otsu: tự động tìm ngưỡng tối ưu để phân chia nền và vật thể.
BINARY_INV: đảo màu — pixel tối trở thành trắng (255), và ngược lại.
b1 là ảnh nhị phân đảo màu, chuẩn bị cho các bước tách vật thể.

b2 = cv2.erode(b1, None, iterations=2)

Làm co nhỏ vùng trắng, giúp loại bỏ các nhiễu nhỏ, rời rạc.
iterations=2: thực hiện erosion 2 lần liên tiếp.

dist_trans = cv2.distanceTransform(b2, 2, 3)

Tính khoảng cách của mỗi pixel trắng tới pixel đen gần nhất.
Kết quả: điểm ở giữa vật thể có giá trị lớn hơn (càng xa viền càng lớn).
Dùng để xác định trung tâm vật thể trong bước tiếp theo.

thresh, dt = cv2.threshold(dist_trans, 1, 255, cv2.THRESH_BINARY)

Áp dụng ngưỡng cho ảnh distance transform.
Chuyển thành ảnh nhị phân (dt) để chỉ giữ lại những vùng có khoảng cách > 1 (tức là các vùng bên trong vật thể).
Đây là vùng foreground (vật thể) sẽ dùng để phân vùng.
labelled, ncc = label(dt)

label(dt): gán số thứ tự (label) cho từng vùng trắng tách biệt trong ảnh nhị phân.
labelled: ảnh gán nhãn, mỗi vùng có 1 giá trị riêng biệt.
ncc: số lượng vùng tìm được.

labelled = labelled.astype(np.int32)

Watershed trong OpenCV yêu cầu ảnh nhãn đầu vào phải là kiểu int32.

cv2.watershed(data, labelled)

cv2.watershed(...) sử dụng ảnh màu gốc (data) và ảnh nhãn (labelled) để tách riêng các vật thể dính liền.
Các viền giữa các vùng sẽ được đánh dấu là -1.

b = Image.fromarray(labelled)

Chuyển ảnh labelled (kiểu int32) sang ảnh PIL để hiển thị.
⚠️ Lưu ý: ảnh kiểu int32 có thể gây hiển thị sai hoặc không rõ — do dữ liệu không nằm trong khoảng 0–255.

plt.imshow(b)
plt.show()

Hiển thị ảnh phân vùng sau khi áp dụng watershed.
Mỗi vùng vật thể sẽ có giá trị khác nhau — giúp phân biệt được vật thể với nền và giữa các vật thể.

Bài 4

data = Image.open('./exercise/dalat.jpg').convert('L')

Image.open(...): mở ảnh dalat.jpg từ thư mục exercise.
.convert('L'): chuyển ảnh sang chế độ xám (grayscale), mỗi pixel có giá trị từ 0–255.

b = nd.binary_dilation(data, iterations=50)

nd.binary_dilation(...): thực hiện giãn nở ảnh nhị phân — tức là làm mở rộng các vùng sáng (pixel "1") ra bên ngoài.
iterations=50: lặp phép giãn nở 50 lần, làm các vùng sáng lan rộng rất nhiều.
Tuy nhiên:
Ở đây data là ảnh xám (giá trị 0–255) chứ không phải nhị phân (True/False).
Hàm binary_dilation sẽ ngầm coi mọi giá trị khác 0 là True, tức là ảnh xám được xem như ảnh nhị phân ngầm định.
Vì thế toàn bộ các vùng có giá trị > 0 đều sẽ bị giãn nở.

c = Image.fromarray(b)

b là mảng Boolean (True/False) sau giãn nở.
Image.fromarray(b): chuyển về ảnh PIL.Image.
⚠️ Tuy nhiên:
Image.fromarray(...) sẽ không hiển thị đúng ảnh nếu mảng là kiểu bool.
Nó cần ảnh có kiểu dữ liệu như uint8 với giá trị 0–255.

c.show()
plt.imshow(c, cmap='gray')
plt.show()

c.show(): mở ảnh bằng trình xem ảnh mặc định của hệ điều hành.
plt.imshow(c, cmap='gray'): hiển thị ảnh bằng matplotlib với bảng màu xám.
plt.show(): vẽ ảnh ra màn hình.

Bài 5

data = Image.open('./exercise/dalat.jpg').convert('L')
data = np.array(data)

Image.open(...): mở ảnh dalat.jpg từ thư mục exercise.
.convert('L'): chuyển ảnh sang chế độ grayscale (ảnh xám).
np.array(...): chuyển ảnh từ kiểu PIL.Image sang mảng NumPy, để xử lý bằng toán học.


s = [[0, 1, 0],
     [1, 1, 1],
     [0, 1, 0]]

Đây là structuring element 3x3 (phần tử cấu trúc) dùng cho phép toán hình học.
Dạng chữ thập (+) thường dùng để xử lý liên kết 4 chiều.
Giá trị 1 xác định vùng ảnh sẽ được xem xét trong mỗi bước giãn/ăn mòn.

b = nd.binary_opening(data, structure=s, iterations=25)

binary_opening(...): gồm 2 bước liên tiếp:
Erosion (co lại): loại bỏ điểm nhiễu nhỏ.
Dilation (mở rộng lại): giữ nguyên kích thước các vùng hợp lệ.
structure=s: sử dụng phần tử cấu trúc đã định nghĩa.
iterations=25: lặp lại phép mở 25 lần, giúp loại bỏ nhiều nhiễu nhỏ và làm mịn biên rõ hơn.
Ảnh đầu vào là grayscale (giá trị 0–255), nhưng hàm này sẽ coi các pixel > 0 là True (trắng) → ảnh xám được xử lý như ảnh nhị phân ngầm định.

c = Image.fromarray((b * 255).astype(np.uint8))

b là ảnh nhị phân kiểu bool (True/False).
b * 255: chuyển True → 255, False → 0 để có ảnh trắng đen đúng chuẩn.
.astype(np.uint8): ép kiểu dữ liệu sang uint8 để có thể hiển thị bằng PIL.
Image.fromarray(...): chuyển mảng NumPy thành đối tượng ảnh PIL.Image.

c.show()
plt.imshow(c, cmap='gray')
plt.show()

c.show(): mở ảnh bằng ứng dụng xem ảnh mặc định trên máy.
plt.imshow(c, cmap='gray'): hiển thị ảnh bằng đồ họa matplotlib, với chế độ thang độ xám.
plt.show(): hiển thị hình ra màn hình.

Bài 6

data = Image.open('./exercise/dalat.jpg').convert('L')
data = np.array(data)

Image.open(...): mở ảnh dalat.jpg từ thư mục exercise.
.convert('L'): chuyển sang ảnh xám (mỗi pixel có giá trị 0–255).
np.array(...): chuyển ảnh sang mảng NumPy để xử lý bằng toán học.

s = [[0, 1, 0],
     [1, 1, 1],
     [0, 1, 0]]

Đây là structuring element dạng dấu cộng (+), được dùng để xác định vùng ảnh lân cận cho mỗi bước erosion.
Còn gọi là liên kết 4 hướng (4-connected).

ry_erosion(data, structure=s, iterations=50)

binary_erosion(...) là phép bào mòn ảnh nhị phân:
Làm co lại các vùng trắng (pixel = True hoặc > 0).
Giúp loại bỏ các chi tiết nhỏ, mỏng hoặc nhiễu.
structure=s: dùng phần tử cấu trúc đã khai báo ở trên.
iterations=50: lặp phép bào mòn 50 lần → kết quả sẽ co nhỏ mạnh các vùng trắng.
Lưu ý: data là ảnh xám (0–255), nhưng hàm sẽ ngầm coi mọi giá trị > 0 là True.

c = Image.fromarray((b * 255).astype(np.uint8))

b là mảng Boolean (True/False) sau khi bào mòn.
b * 255: chuyển True → 255 (trắng), False → 0 (đen).
.astype(np.uint8): chuyển kiểu dữ liệu thành uint8, phù hợp để hiển thị.
Image.fromarray(...): tạo ảnh từ mảng NumPy.

c.show()
plt.imshow(c, cmap='gray')
plt.show()

c.show(): mở ảnh bằng trình xem ảnh mặc định.
plt.imshow(c, cmap='gray'): hiển thị ảnh theo thang độ xám.
plt.show(): hiển thị hình ra màn hình.

Bài 7

data = Image.open('./exercise/dalat.jpg').convert('L')
data = np.array(data)

Image.open(...): đọc ảnh từ đường dẫn ./exercise/dalat.jpg.
.convert('L'): chuyển ảnh sang grayscale (ảnh xám, 1 kênh, giá trị 0–255).
np.array(...): chuyển ảnh từ PIL.Image sang mảng NumPy để xử lý.

s = [[0, 1, 0],
     [1, 1, 1],
     [0, 1, 0]]

Đây là structuring element hình dấu cộng (4 hướng).
Dùng để xác định vùng lân cận cho các phép toán hình học.
Là phần tử dùng trong phép closing bên dưới.

b = nd.binary_closing(data, structure=s, iterations=50)

binary_closing(...) là phép toán gồm 2 bước:
Dilation (giãn nở): mở rộng các vùng trắng.
Erosion (bào mòn): thu nhỏ lại các vùng đã mở rộng để khôi phục hình dạng gốc.
Mục tiêu:
Làm đầy các lỗ nhỏ (đen) bên trong vùng trắng.
Nối các vết nứt nhỏ trong vùng foreground.
structure=s: dùng phần tử cấu trúc hình dấu cộng.
iterations=50: lặp lại 50 lần → ảnh sẽ được vá rất mạnh.
⚠️ Lưu ý:
data là ảnh xám (grayscale), nhưng hàm binary_closing ngầm coi các giá trị > 0 là True, tức là vùng trắng.
Vì thế bạn đang áp dụng closing như với ảnh nhị phân giả định.

c = Image.fromarray((b * 255).astype(np.uint8))

b là mảng Boolean (True/False) kết quả sau closing.
b * 255: chuyển True thành 255 (trắng), False thành 0 (đen).
.astype(np.uint8): chuyển kiểu dữ liệu về uint8 để có thể hiển thị đúng.
Image.fromarray(...): chuyển từ mảng sang ảnh PIL.

c.show()
plt.imshow(c, cmap='gray')
plt.show()

c.show(): hiển thị ảnh bằng ứng dụng xem ảnh mặc định trên hệ điều hành.
plt.imshow(c, cmap='gray'): hiển thị ảnh bằng đồ họa matplotlib theo thang độ xám.
plt.show(): vẽ ra màn hình.

Bài 8

if img is None:
    raise FileNotFoundError("Không tìm thấy ảnh da_lat.jpg trong thư mục exercise.")

Nếu ảnh không tồn tại hoặc sai tên → in lỗi và dừng chương trình.

x, y, w, h = 200, 100, 300, 200
region = img[y:y+h, x:x+w]

Cắt một vùng con từ ảnh có kích thước 300x200 bắt đầu từ điểm (200, 100).
region là mảng ảnh con chứa vùng LangBiang cần xử lý.

translated_region = np.zeros_like(region)
if region.shape[1] > 100:
    translated_region[:, 100:] = region[:, :-100]

Tạo một mảng mới cùng kích thước, toàn màu đen (0).
Sao chép phần nội dung của ảnh sang bên phải 100 pixel:
region[:, :-100] lấy phần từ trái.
translated_region[:, 100:] là phần đích sau khi dịch phải.
Kết quả: ảnh con được tịnh tiến phải 100px, phần bên trái bị đen.

region_norm = translated_region / 255.0
_, binary = cv2.threshold(region_norm, 0.3, 1.0, cv2.THRESH_BINARY)

region_norm: chuẩn hóa ảnh về [0, 1] để dễ xử lý với ngưỡng nhỏ như 0.3.
cv2.threshold(..., 0.3, 1.0, cv2.THRESH_BINARY):
Nếu pixel > 0.3 → gán giá trị 1.0.
Ngược lại → gán 0.
Kết quả: binary là ảnh nhị phân với giá trị 0 và 1.

binary_uint8 = (binary * 255).astype(np.uint8)
save_path = 'lang_biang.jpg'
cv2.imwrite(save_path, binary_uint8)

binary có giá trị 0.0 và 1.0 → nhân 255 để về 0 và 255.
astype(np.uint8): chuyển kiểu dữ liệu sang uint8, ảnh trắng đen chuẩn.
cv2.imwrite(...): lưu ảnh sau xử lý với tên lang_biang.jpg.

plt.imshow(binary_uint8, cmap='gray')
plt.title("LangBiang đã xử lý")
plt.axis('off')
plt.show()

imshow(..., cmap='gray'): hiển thị ảnh theo thang độ xám.
axis('off'): ẩn trục tọa độ.
show(): vẽ ảnh ra màn hình.

Bài 9

img_path = 'exercise/dalat.jpg'
img = cv2.imread(img_path, cv2.IMREAD_GRAYSCALE)
if img is None:
    raise FileNotFoundError("Không tìm thấy ảnh dalat.jpg.")

Đọc ảnh từ đường dẫn 'exercise/dalat.jpg' ở chế độ grayscale (ảnh xám, 1 kênh).
Nếu ảnh không tồn tại → in lỗi và thoát chương trình.

x, y, w, h = 250, 250, 300, 200
region = img[y:y+h, x:x+w]

Cắt vùng ảnh có kích thước 300x200 bắt đầu từ tọa độ (250, 250).
region sẽ chứa phần ảnh nhỏ đại diện cho khu vực Hồ Xuân Hương.
Bạn có thể điều chỉnh tọa độ nếu chưa đúng vị trí trong ảnh thật.

center = (w // 2, h // 2)
matrix = cv2.getRotationMatrix2D(center, 45, 1.0)
rotated = cv2.warpAffine(region, matrix, (w, h), flags=cv2.INTER_LINEAR)

center = (150, 100): tính tâm của vùng ảnh con để xoay quanh nó.
cv2.getRotationMatrix2D(...): tạo ma trận biến đổi affine để xoay 45 độ, giữ nguyên kích thước.
cv2.warpAffine(...): áp dụng ma trận xoay lên ảnh.
flags=cv2.INTER_LINEAR: dùng nội suy tuyến tính để giữ độ mượt.
Kết quả là rotated – ảnh vùng Hồ Xuân Hương đã được xoay 45 độ.

adaptive = cv2.adaptiveThreshold(
    rotated,
    maxValue=255,
    adaptiveMethod=cv2.ADAPTIVE_THRESH_MEAN_C,
    thresholdType=cv2.THRESH_BINARY,
    blockSize=11,
    C=60
)

adaptiveThreshold(...): phương pháp phân ngưỡng theo từng vùng nhỏ của ảnh.
adaptiveMethod=cv2.ADAPTIVE_THRESH_MEAN_C: dùng trung bình các pixel lân cận làm ngưỡng.
blockSize=11: mỗi vùng nhỏ 11x11 pixel được xét riêng biệt.
C=60: sau khi tính trung bình vùng lân cận, trừ thêm 60 → pixel phải sáng hơn giá trị này mới thành trắng.
thresholdType=cv2.THRESH_BINARY: ngưỡng kiểu nhị phân (trắng đen rõ ràng).
maxValue=255: giá trị khi pixel vượt ngưỡng sẽ là 255 (trắng).
Kết quả là ảnh trắng đen với độ tương phản cao, làm nổi bật đường nét trong ảnh xoay.

cv2.imwrite('ho_xuan_huong.jpg', adaptive)
plt.imshow(adaptive, cmap='gray')
plt.title("Hồ Xuân Hương sau xử lý")
plt.axis('off')
plt.show()

cv2.imwrite(...): lưu ảnh sau xử lý với tên ho_xuan_huong.jpg.
plt.imshow(...): hiển thị ảnh bằng matplotlib với màu thang xám (gray).
plt.title(...): đặt tiêu đề cho hình ảnh.
axis('off'): ẩn trục tọa độ để ảnh rõ hơn.
plt.show(): hiển thị ra màn hình.

Bài 10

img_path = 'exercise/dalat.jpg'
img = cv2.imread(img_path, cv2.IMREAD_GRAYSCALE)
if img is None:
    raise FileNotFoundError("Không tìm thấy ảnh da_lat.jpg.")

Ảnh được đọc từ tệp 'exercise/dalat.jpg' ở chế độ ảnh xám.
Nếu không tìm thấy ảnh, chương trình sẽ dừng và báo lỗi.

x, y, w, h = 350, 150, 200, 200
region = img[y:y+h, x:x+w]

Cắt một vùng 200x200 pixel bắt đầu từ tọa độ (350, 150) trong ảnh gốc.
Vùng này đại diện cho Quảng trường Lâm Viên (bạn có thể điều chỉnh để chính xác hơn với ảnh thật).

rows, cols = region.shape
map_x = np.zeros_like(region, dtype=np.float32)
map_y = np.zeros_like(region, dtype=np.float32)
for i in range(rows):
    for j in range(cols):
        map_x[i, j] = j + 10 * np.sin(i / 20.0)
        map_y[i, j] = i

map_x, map_y: tạo ra bản đồ tọa độ để biến đổi hình học.
Với mỗi pixel (i, j):
x được dịch sang trái/phải theo hàm sin → tạo dạng sóng ngang.
y giữ nguyên để không làm thay đổi chiều dọc.
Kết quả là một biến dạng nhẹ theo chiều ngang, nhìn như gợn sóng nước.

mapped = cv2.remap(region, map_x, map_y, interpolation=cv2.INTER_LINEAR)

cv2.remap(...) sẽ ánh xạ lại ảnh theo map_x, map_y.
INTER_LINEAR: dùng nội suy tuyến tính giúp mượt hơn.
Kết quả là ảnh mapped đã bị biến dạng theo dạng sóng.

_, binary = cv2.threshold(mapped, 127, 255, cv2.THRESH_BINARY)

Áp dụng phân ngưỡng toàn cục với ngưỡng 127:
Pixel > 127 → trắng (255)
Ngược lại → đen (0)
Tạo ra ảnh nhị phân rõ nét giữa nền và vật thể.

kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (5, 5))
closed = cv2.morphologyEx(binary, cv2.MORPH_CLOSE, kernel)

Structuring Element hình chữ nhật 5x5 được dùng cho phép toán hình học.
cv2.MORPH_CLOSE: thực hiện:
Dilation (giãn nở): mở rộng các vùng trắng.
Erosion (bào mòn): làm co lại, giữ biên gốc.
Mục tiêu:
Lấp lỗ đen nhỏ bên trong vùng trắng.
Làm trơn biên vật thể.

cv2.imwrite('quang_truong_lam_vien.jpg', closed)
plt.imshow(closed, cmap='gray')
plt.title("Quảng trường Lâm Viên sau xử lý")
plt.axis('off')
plt.show()

Ảnh sau xử lý được lưu vào tệp quang_truong_lam_vien.jpg.
imshow(..., cmap='gray'): hiển thị ảnh theo thang xám.
plt.axis('off'): tắt trục tọa độ để ảnh đẹp hơn.
plt.show(): vẽ ảnh ra màn hình.

Bài 11

a. rotate_image: Xoay ảnh
def rotate_image(img, angle=45):

→ Xoay ảnh một góc 45 độ quanh tâm ảnh.

b. scale_image: Phóng to ảnh
def scale_image(img, fx=1.5, fy=1.5):

→ Phóng ảnh lên 1.5 lần (cả chiều ngang và dọc).

c. shift_image: Di chuyển ảnh
def shift_image(img, tx=50, ty=50):

→ Dịch ảnh sang phải 50px và xuống 50px.

d. coordinate_mapping: Biến dạng ảnh dạng sóng
def coordinate_mapping(img):

→ Làm ảnh bị gợn sóng nhẹ theo chiều ngang.

e. adaptive_thresholding: Phân ngưỡng thích nghi
def adaptive_thresholding(img):

→ Tách vật thể bằng cách chia ảnh thành vùng nhỏ và dùng ngưỡng riêng cho mỗi vùng.

f. binary_dilation: Giãn ảnh
def binary_dilation(img):

→ Làm vùng trắng to ra (lấp lỗ, nối vùng).

g. binary_erosion: Bào mòn ảnh
def binary_erosion(img):

→ Làm vùng trắng nhỏ lại (xóa chi tiết nhỏ).

h. otsu_threshold: Phân ngưỡng tự động
def otsu_threshold(img):

→ Tự động chọn ngưỡng để tách vật thể khỏi nền.

img = cv2.imread('exercise/dalat.jpg', cv2.IMREAD_GRAYSCALE)

→ Mở ảnh trắng đen từ file 'dalat.jpg'.

print("=== MENU CHỌN XỬ LÝ ẢNH ===")
...

→ In ra danh sách các xử lý cho người dùng chọn như:
a = làm gợn sóng ảnh
b = xoay ảnh
e = phân ngưỡng thích nghi, ...

choice = input("Nhập lựa chọn (vd: b,h): ")

→ Người dùng nhập các lựa chọn ví dụ "b,h" (xoay + otsu).


for ch in choice:
    ...

→ Áp dụng lần lượt các xử lý mà người dùng chọn lên ảnh.

cv2.imwrite("output_menu.jpg", processed)
plt.imshow(processed, cmap='gray')
plt.show()

→ Lưu ảnh đã xử lý và hiển thị lên màn hình.






