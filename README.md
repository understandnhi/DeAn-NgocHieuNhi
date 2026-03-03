# Phân Tích Các Yếu Tố Ảnh Hưởng Đến Doanh Thu Toàn Cầu (IMDB Movies)

## Nội Dung Dự Án

Dự án này thực hiện phân tích các yếu tố ảnh hưởng đến doanh thu toàn cầu của các bộ phim dựa trên tập dữ liệu IMDB. Sử dụng kỹ thuật làm sạch dữ liệu, phân tích thống kê,... để xác định vai trò của từng biến trong việc dự đoán thành công thương mại của phim.

## Mục Tiêu Phân Tích

1. **Làm sạch và xử lý dữ liệu**: Xử lý các giá trị tiền tệ, thời gian, phát hiện và xử lý outliers
2. **Xác định tầm quan trọng biến**: Sử dụng Random Forest để phân tích importance của các biến (≥ 0.05)
3. **Phân tích biến liên tục**: Chuẩn hóa dữ liệu, hình ảnh hóa, kiểm tra phân phối chuẩn, tương quan Spearman, VIF, mô hình GLM
4. **Phân tích biến rời rạc**: Vẽ biểu đồ đường doanh thu theo năm phát hành
5. **Đưa ra các đề xuất** dựa trên kết quả phân tích

## Tập Dữ Liệu

- **Tập tin gốc**: `IMDB_Movies_Dataset.csv`
- **Tập tin đã xử lý**: `cleaned_data.csv`
- **Số lượng bộ phim**: ~1,926 phim sau khi làm sạch dữ liệu
- **Các cột chính**:
  - **Biến phụ thuộc**: `Worldwide Gross (USD)` - Doanh thu toàn cầu
  - **Biến độc lập liên tục**: Budget, Runtime, Average Rating, Metascore, Release_Year
  - **Biến độc lập rời rạc**: Director, Writer, Country of Origin, Languages

## Các Bước Xử Lý Dữ Liệu

### 1. Làm Sạch Dữ Liệu
- Loại bỏ các dòng bị thiếu dữ liệu
- Xóa các dòng trùng lặp
- Chuyển đổi tiền tệ sang USD (xử lý các loại tiền cũ như ITL, ATS, DEM, v.v)
- Chuyển Release Date sang định dạng datetime, tách thành năm và tháng
- Chuyển Runtime sang đơn vị phút
- Xử lý outliers bằng phương pháp IQR (InterQuantile Range)

### 2. Phân Loại Biến
- Các cột kiểu object (object type) được phân loại thành biến categorical
- Các cột kiểu số nhưng có ít giá trị khác nhau được coi là categorical
- Sử dụng **Top-15 Encoding** để gom nhóm các giá trị làm phim thành "Other"

### 3. Chuẩn Hóa Dữ Liệu
- Sử dụng **StandardScaler (Z-score normalization)** cho các biến liên tục

## Kết Quả Chính

### Tầm Quan Trọng Biến (Random Forest)
- **Budget (Ngân sách)**: 66.3% - Yếu tố ảnh hưởng lớn nhất
- **Runtime (Thời lượng)**: 6.5%
- **Average Rating (Đánh giá từ khán giả)**: 6.5%
- **Metascore (Đánh giá từ phê bình)**: ~6.0%
- **Release_Year (Năm phát hành)**: ~5.0%

### Phân Tích Tương Quan Spearman
| Biến | Hệ số tương quan | Ý nghĩa |
|------|-----------------|---------|
| Budget | 0.8 | **Mạnh** - Ngân sách cao ↔ Doanh thu cao |
| Runtime | 0.3 | **Trung bình** |
| Average Rating | 0.1 | **Yếu** |
| Metascore | -0.1 | **Không đáng kể** |

### Mô Hình GLM (Hồi Quy Tuyến Tính)
- **R² (Pseudo)**: 0.8428 - Mô hình giải thích 84.28% phương sai
- **Budget**: +106M USD/đơn vị (p < 0.001) ✓ Rất có ý nghĩa
- **Average Rating**: +24.09M USD/điểm (p < 0.001) ✓ Rất có ý nghĩa
- **Runtime**: -11.57M USD/phút (p < 0.001) ✓ Có ý nghĩa (tác động ngược)
- **Metascore**: +2.56M USD/điểm (p = 0.189) ✗ Không có ý nghĩa thống kê

### Xu Hướng Doanh Thu Theo Năm
- Doanh thu trung bình có xu hướng **tăng mạnh từ năm 1995 trở đi**
- Phim phát hành gần đây có doanh thu cao hơn đáng kể
- Phản ánh sự phát triển mạnh mẽ của ngành công nghiệp điện ảnh toàn cầu

## Công Cụ & Thư Viện Sử Dụng

```python
- pandas: Xử lý dữ liệu
- numpy: Tính toán số học
- matplotlib & seaborn: Hình ảu hóa dữ liệu
- scikit-learn: Mô hình Random Forest, StandardScaler
- statsmodels: Mô hình GLM
- scipy: Kiểm định Shapiro-Wilk, tương quan Spearman
- requests: Lấy tỷ giá từ API
- forex_python: Chuyển đổi tiền tệ
- regex (re): Xử lý chuỗi (string manipulation)
```

## Cách Sử Dụng

### Yêu Cầu Cài Đặt
```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy requests forex-python
```

### Chạy Phân Tích
1. Mở file `EDA3.ipynb` trong Jupyter Notebook hoặc VS Code
2. Chạy theo thứ tự từ trên xuống dưới
3. Xem kết quả hình ảnh hóa và số liệu thống kê

## Kết Luận Chính

1. **Ngân sách là yếu tố quyết định**: Với 66.3% tầm quan trọng và hệ số 106M USD, ngân sách sản xuất là yếu tố mạnh nhất ảnh hưởng đến doanh thu toàn cầu.

2. **Chất lượng nội dung quan trọng**: Average Rating có mối quan hệ tích cực đáng kể (+24.09M USD/điểm), cho thấy phim chất lượng cao sẽ thu hút khán giả tốt hơn.

3. **Thời lượng phim cần cân bằng**: Phim quá dài có thể ảnh hưởng tiêu cực đến doanh thu (-11.57M USD/phút), nên nên giữ trong khoảng 90-120 phút.

4. **Thời điểm phát hành ảnh hưởng**: Phim phát hành từ 1995 trở đi có doanh thu cao hơn đáng kể, phản ánh sự phát triển ngành.

5. **Metascore không quyết định**: Đánh giá từ giới phê bình không có ý nghĩa thống kê trong mô hình, điều này có thể do khán giả đại chúng quan tâm hơn đến Average Rating.

## Đề Xuất

✅ **Tăng cường đầu tư vào ngân sách sản xuất và marketing**
✅ **Duy trì thời lượng phim hợp lý (~90-120 phút)**
✅ **Phát hành vào thời điểm cao điểm (mùa hè, lễ, cuối tuần dài)**
✅ **Tập trung vào chất lượng nội dung để nâng Average Rating**
✅ **Tận dụng công nghệ mới và nền tảng phân phối toàn cầu**


**Cập nhật lần cuối**: Tháng 3, 2026
