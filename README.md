# Chẩn đoán Kinh doanh & Dự báo Doanh thu Dựa trên Dữ liệu (Data-Driven Business Diagnostics & Revenue Forecasting)

**Dự án:** Datathon 2026 - Vòng 1  
**Tác giả:** Đoàn Hùng  
**Công nghệ sử dụng:** Python, XGBoost, SQLite, LaTeX (NeurIPS Style)

---

## 1. Tổng quan dự án (Project Overview)
Dự án này tập trung vào việc chẩn đoán sức khỏe kinh doanh và dự báo tài chính cho một doanh nghiệp thương mại điện tử thời trang tại Việt Nam trong giai đoạn 2012 - 2022. Mục tiêu chính bao gồm:
* **Phân tích dữ liệu khám phá (EDA):** Khai thác 15 bảng dữ liệu để tìm ra các quy luật về nhu cầu khách hàng theo vùng miền, lợi nhuận phân khúc và ảnh hưởng của khuyến mãi đến biên lợi nhuận.
* **Học máy (Machine Learning):** Xây dựng hệ thống dự báo song hành (Parallel Forecasting) cho **Revenue** (Doanh thu) và **COGS** (Giá vốn) trong 548 ngày tiếp theo.

## 2. Cấu trúc tập dữ liệu (Dataset Structure)
Dữ liệu được quản lý tập trung trong file SQLite `datathon_2026.db` bao gồm các nhóm bảng chính:
* **Dữ liệu danh mục (Master Data):** `customers`, `products`, `geography`.
* **Dữ liệu giao dịch (Transactions):** `orders`, `order_items`, `payments`.
* **Dữ liệu vận hành (Operations):** `inventory`, `web_traffic`, `shipments`, `returns`.

## 3. Phương pháp luận kỹ thuật (Technical Methodology)

### Kỹ thuật đặc trưng (Feature Engineering)
Mô hình sử dụng các đặc trưng được trích xuất tối ưu để nắm bắt tính mùa vụ và xu hướng:
* **Thời gian (Temporal):** Thứ trong tuần (`day_of_week`), tháng (`month`)[cite: 1].
* **Biến trễ (Lags):** $Lag\_1$, $Lag\_7$ (giá trị trễ 1 ngày và 7 ngày để bắt chu kỳ tuần)[cite: 1].
* **Trung bình trượt (Window Functions):** `rolling_mean_7` để làm mượt dữ liệu[cite: 1].

### Chiến lược Dự báo Song hành (Parallel Forecasting Strategy)
Điểm khác biệt cốt lõi là quy trình dự báo song hành tích hợp trong vòng lặp cuốn chiếu để đảm bảo tính nhất quán kinh tế[cite: 1]:
1. Dự báo **COGS** cho ngày $T$ dựa trên các biến trễ[cite: 1].
2. Nạp giá trị COGS vừa dự báo làm đầu vào (feature) cho mô hình **Revenue**[cite: 1].
3. Dự báo **Revenue** cho ngày $T$[cite: 1].
4. Cập nhật cả hai kết quả để tiếp tục vòng lặp cho ngày $T+1$[cite: 1].

## 4. Hiệu suất mô hình (Performance)
Mô hình XGBoost đạt độ tin cậy cao trên tập kiểm thử[cite: 1]:
* **Hệ số xác định ($R^2$):** $\approx 0.97$[cite: 1].
* **Phương pháp kiểm thử:** `TimeSeriesSplit` để đảm bảo không rò rỉ dữ liệu tương lai[cite: 1].

## 5. Hướng dẫn sử dụng (Usage)

### Yêu cầu hệ thống
Để chạy file notebook `Datathon.ipynb`, cần cài đặt các thư viện sau:
```bash
pip install pandas numpy xgboost matplotlib seaborn scikit-learn
