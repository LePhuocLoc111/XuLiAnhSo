Bài 1: 
Nhập thư viện:
import os, from PIL import Image, import numpy as np, import matplotlib.pyplot as plt, import cv2

Định nghĩa các hàm biến đổi:

image_inverse(img): Tạo ảnh âm bản (255 - pixel).
gamma_correction(img, gamma): Biến đổi gamma để điều chỉnh độ sáng/tương phản.
log_transformation(img): Biến đổi logarit để làm nổi bật vùng tối.
histogram_equalization(img): Cân bằng histogram (dùng OpenCV).
contrast_stretching(img): Kéo dãn tương phản về toàn dải [0,255].
Hàm process_and_save:

Đọc ảnh, chuyển sang xám, áp dụng biến đổi, lưu kết quả vào thư mục output, hiển thị ảnh.
Hàm main:

Đọc ảnh từ đường dẫn, lần lượt áp dụng các phép biến đổi, lưu và hiển thị kết quả.

Bài 2:
Nhập thư viện:
import os, import numpy as np, import matplotlib.pyplot as plt, from PIL import Image

Định nghĩa các hàm:

fast_fourier_transform(img): Biến đổi Fourier, trả về phổ biên độ.
butterworth_lowpass(img, d0, n): Lọc thông thấp Butterworth.
butterworth_highpass(img, d0, n): Lọc thông cao Butterworth.
Hàm process_and_collect:

Đọc ảnh, áp dụng từng phép biến đổi, lưu kết quả vào thư mục output.
Hàm main:

Lấy danh sách ảnh trong thư mục exercise, áp dụng tất cả phép biến đổi, lưu và hiển thị kết quả.

Bài 3: 
Nhập thư viện:
import os, import random, import numpy as np, import matplotlib.pyplot as plt, from PIL import Image

Định nghĩa các hàm:

Các hàm biến đổi tần số như bài 2.
shuffle_rgb(img_rgb): Đảo trộn thứ tự kênh màu RGB.
Hàm main:

Lấy danh sách ảnh trong exercise, đảo trộn kênh màu, chuyển sang xám, chọn ngẫu nhiên một phép biến đổi tần số, lưu và hiển thị kết quả.

Bài 4: 
Nhập thư viện:
import os, import random, import numpy as np, import matplotlib.pyplot as plt, from PIL import Image, ImageFilter

Định nghĩa các hàm:

butterworth_lowpass, butterworth_highpass: Biến đổi tần số.
shuffle_rgb(img_rgb): Đảo trộn kênh màu RGB.
apply_min_filter(img_array): Lọc Min 3x3.
apply_max_filter(img_array): Lọc Max 3x3.
Hàm main:

Lấy danh sách ảnh, đảo trộn kênh màu, chuyển sang xám, chọn ngẫu nhiên phép biến đổi tần số và bộ lọc, áp dụng, lưu và hiển thị kết quả.