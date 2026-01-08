# LAB 4 – DỰ BÁO CHUỖI THỜI GIAN: CHẤT LƯỢNG KHÔNG KHÍ (PM2.5)

**Môn học:** Khai phá dữ liệu (Data Mining)  
**Nhóm thực hiện:** Nhóm 2  
**Dataset:** Beijing Multi-Site Air Quality Dataset (PRSA2017)  
**Trạm phân tích:** Aotizhongxin  
**Mục tiêu:** Dự báo nồng độ bụi mịn PM2.5 theo giờ dựa trên dữ liệu lịch sử.

---

## 1. Mô tả dữ liệu

Bộ dữ liệu PRSA2017 bao gồm dữ liệu chất lượng không khí và thời tiết được thu thập theo **tần suất giờ** tại nhiều trạm quan trắc ở Bắc Kinh trong giai đoạn **2013–2017**.

Trong bài lab này, nhóm tập trung phân tích:
- **Trạm:** Aotizhongxin
- **Biến mục tiêu:** Nồng độ bụi mịn **PM2.5**

**Thông tin dữ liệu:**
- Số dòng: **420,768**
- Số cột ban đầu: **18**
- Độ phân giải thời gian: **Theo giờ**

---

## 2. Tiền xử lý dữ liệu & Phân tích khám phá (Question 1)

### 2.1. Tiền xử lý dữ liệu
Các bước tiền xử lý chính bao gồm:
- Xử lý các giá trị thiếu (missing values)
- Tạo cột thời gian `datetime` và sắp xếp theo thứ tự thời gian
- Sinh các đặc trưng thời gian: giờ, ngày, tháng, năm
- Tạo các đặc trưng trễ (lag features): `lag_1`, `lag_3`, `lag_24`
- Xây dựng nhãn phân lớp chất lượng không khí (`aqi_class`) dựa trên PM2.5 trung bình 24 giờ

**Kích thước dữ liệu sau xử lý:**
- **420,768 dòng × 55 đặc trưng**

### 2.2. Phân tích khám phá dữ liệu (EDA)
Một số nhận xét chính:
- Chuỗi PM2.5 có biến động mạnh và xuất hiện nhiều đỉnh ô nhiễm đột biến
- Phân bố các mức chất lượng không khí có hiện tượng mất cân bằng nhẹ, trong đó mức “Không tốt cho sức khỏe” chiếm tỷ lệ lớn
- Các đặc trưng trễ gây ra giá trị thiếu ở đầu chuỗi thời gian

---

## 3. Mô hình hồi quy cơ sở (Question 2 – Regression)

### 3.1. Phương pháp
Nhóm sử dụng **mô hình Hồi quy tuyến tính (Linear Regression)** để dự báo nồng độ PM2.5 tại thời điểm *t+1*.

- Đặc trưng đầu vào: các biến trễ (`lag_1`, `lag_3`, `lag_24`)
- Tập huấn luyện: dữ liệu từ năm 2013 đến 2016
- Tập kiểm tra: dữ liệu từ ngày 01/01/2017 trở đi

### 3.2. Kết quả đánh giá

| Chỉ số | Giá trị |
|------|--------|
| **RMSE** | **25.32** |
| **MAE** | **12.32** |
| **R² Score** | **0.949** |

Mô hình hồi quy cho kết quả dự báo rất tốt, bám sát dữ liệu thực tế và bắt được các đỉnh ô nhiễm lớn.

---

## 4. Dự báo chuỗi thời gian với ARIMA (Question 3)

### 4.1. Kiểm tra tính dừng & lựa chọn tham số
- Tính dừng của chuỗi được kiểm tra bằng thống kê trượt và kiểm định Augmented Dickey-Fuller (ADF)
- Chuỗi thể hiện rõ yếu tố mùa vụ theo chu kỳ ngày
- Các tham số *(p, d, q)* được xác định dựa trên biểu đồ ACF, PACF và Grid Search theo tiêu chí AIC

### 4.2. Kết quả mô hình ARIMA
- Cấu hình tối ưu: **ARIMA(1, 0, 3)**

| Chỉ số | Giá trị |
|------|--------|
| **RMSE** | **104.10** |
| **MAE** | **77.69** |

Mô hình ARIMA cho dự báo mượt hơn nhưng không theo kịp các biến động mạnh và đột ngột của nồng độ PM2.5.

---

## 5. So sánh mô hình & Kết luận

### 5.1. So sánh hiệu quả

| Mô hình | RMSE | Nhận xét |
|------|------|---------|
| **Hồi quy (dựa trên Lag)** | **25.32** | Hiệu quả nhất |
| **ARIMA** | 104.10 | Kém hơn do dữ liệu nhiều nhiễu |

### 5.2. Kết luận
- Với bài toán dự báo PM2.5 ngắn hạn (1 giờ), mô hình hồi quy sử dụng đặc trưng trễ cho kết quả vượt trội so với ARIMA
- Nguyên nhân chính là PM2.5 có mức tự tương quan rất cao ở các thời điểm gần
- ARIMA gặp khó khăn khi xử lý dữ liệu có độ biến động và nhiễu lớn

**Hướng phát triển:**
- Áp dụng các mô hình phi tuyến như Random Forest, XGBoost
- Sử dụng mô hình học sâu (LSTM, GRU)
- Mở rộng sang mô hình SARIMAX với biến ngoại sinh

---

## 6. Cấu trúc thư mục

```text
data/
  raw/
  processed/
notebooks/
  preprocessing_and_eda.ipynb
  regression_modelling.ipynb
  arima_forecasting.ipynb
src/
  timeseries_library.py
  regression_library.py
run_papermill.py
requirements.txt
