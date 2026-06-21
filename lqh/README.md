# Phân tích và Dự đoán Giá Vàng SJC tại Việt Nam

Đồ án môn Khoa học Dữ liệu — GVHD: TS. Nguyễn Văn Huy
Sinh viên: Lương Quang Hà — MSSV: K225480106010

## 0. Mở bằng VS Code (khuyến nghị)

1. Cài extension **"Jupyter"** (và "Python") trong VS Code nếu chưa có.
2. Mở thư mục `project/` (chứa `gold_price_analysis.ipynb` + folder `data/`) bằng VS Code.
3. Mở file `gold_price_analysis.ipynb` → bấm **"Select Kernel"** ở góc trên phải → chọn
   Python interpreter của máy bạn.
4. Mở Terminal trong VS Code, chạy:
   ```bash
   pip install -r requirements.txt
   ```
5. Chạy từng cell từ trên xuống (Shift+Enter), hoặc "Run All".

File `gold_price_analysis.py` vẫn được giữ lại nếu bạn muốn chạy bằng dòng lệnh thay vì notebook
(`python gold_price_analysis.py`) — cả 2 file chứa cùng logic.

## 2. Bổ sung dữ liệu 07/2024 → nay (đáp ứng yêu cầu "đến 2026")

File `data/gold_price_supplement_2024_2026.csv` chứa 6 mốc giá đã kiểm chứng,
**không phải dữ liệu giao dịch hàng ngày**, dùng để nối tiếp dữ liệu Kaggle (dừng ở
18/06/2024) tới hiện tại:

| Ngày | Mua vào | Bán ra | Nguồn |
|---|---|---|---|
| 31/12/2024 | 82.20 | 84.20 | Báo Pháp Luật TP.HCM (plo.vn) |
| 31/12/2025 | 150.80 | 152.80 | webgia.com (nguồn SJC, cập nhật cuối ngày) |
| 29/01/2026 | 144.80 | 147.80 | Simplize (simplize.vn) |
| ~01/04/2026 (đỉnh) | 189.00 | 191.00 | Finhay (190.3tr) + VietnamNet (192.2tr) — **ngày đỉnh chính xác KHÔNG thống nhất giữa nguồn**, đây là ngày tạm đặt |
| 18/05/2026 | 161.30 | 163.80 | vangviet.net |
| 19/06/2026 | 148.80 | 151.30 | Vietnam.vn |

Notebook tự nội suy tuyến tính **chỉ trong khoảng** giữa các mốc này (mức THÁNG),
vẽ bằng nét đứt màu cam để phân biệt rõ với đường dữ liệu thật (đặc, màu xanh).
File `output_extended_2020_2026.png` là kết quả.

⚠️ **Bắt buộc nêu trong báo cáo:** đoạn 07/2024 → nay là **ước lượng nội suy theo
tháng từ tin tức**, không phải số liệu giao dịch thật theo ngày như đoạn 2020-06/2024.
Nếu muốn có dữ liệu daily đầy đủ cho cả giai đoạn này, cần tự thu thập thêm (xem mục 6).

## 3. Về dữ liệu thật đã dùng (Kaggle, 2014-06/2024)

File `gold_price.csv` (đã upload) có cấu trúc:

```
type,buy,sell,update
SJC,76.980,74.980,18/06/2024 08:09:01
PNJ,75.000,73.300,18/06/2024 08:09:01
...
```

- Cột `type`: gồm cả `SJC` và `PNJ` -> script **tự lọc chỉ lấy SJC**.
- Cột `update`: là thời điểm cập nhật trong ngày (nhiều lần/ngày) -> script **tự gộp về 1 dòng/ngày** (lấy giá cập nhật cuối ngày).
- ⚠️ **Phát hiện quan trọng**: cột `buy` trong file thực ra lưu **giá BÁN RA** (giá khách phải trả khi mua, cao hơn), cột `sell` lưu **giá MUA VÀO** (giá khách nhận khi bán, thấp hơn) — NGƯỢC với quy ước "Mua vào/Bán ra" quen thuộc trên báo Việt Nam (đây là quy ước theo góc nhìn khách hàng: buy = hành động khách MUA, sell = hành động khách BÁN). Script tự động kiểm định bằng thống kê (buy > sell ở 99,95% số dòng) và **tự đảo tên cột** cho đúng nghĩa trước khi phân tích. Đây là một điểm rất nên nhắc trong báo cáo (kỹ năng kiểm tra ý nghĩa thực của cột dữ liệu, không chỉ tin vào tên cột).
- **Phạm vi thời gian thật**: 23/06/2014 → 18/06/2024 (khoảng 10 năm). Đề bài mô tả "2020–2026" nhưng dữ liệu thật KHÔNG có 2025–2026. Script tự lọc theo `YEAR_START = 2020` và in cảnh báo rõ về giới hạn này — **cần nêu rõ giới hạn này trong báo cáo/slide thay vì giả vờ có đủ dữ liệu tới 2026**.

## 3. Cấu trúc thư mục đề xuất

```
project/
├── gold_price_analysis.ipynb
├── gold_price_analysis.py
├── requirements.txt
├── README.md
└── data/
    ├── gold_price.csv
    └── gold_price_supplement_2024_2026.csv
```

## 4. Cài thư viện & chạy

```bash
pip install -r requirements.txt
python gold_price_analysis.py
```

Sửa `DATA_PATH` ở đầu file nếu bạn đặt file CSV ở vị trí khác.

## 5. Kết quả đã kiểm chứng với dữ liệu thật (giai đoạn 2020-06/2024)

| Năm | Giá TB (tr/lượng) | Min | Max |
|---|---|---|---|
| 2020 | 51.37 | 42.8 | 61.9 |
| 2021 | 57.46 | 54.5 | 61.9 |
| 2022 | 67.18 | 61.4 | 74.0 |
| 2023 | 68.63 | 66.5 | 79.6 |
| 2024 (đến 18/6) | 81.29 | 74.2 | 92.2 |

- **Tháng giá cao nhất (TB)**: Tháng 5. **Tháng thấp nhất**: Tháng 7.
- **Chênh lệch mua-bán**: trung bình 0.6–0.9 triệu/lượng các năm 2020-2023, tăng vọt lên **2.22 triệu** năm 2024 — phản ánh thị trường biến động mạnh khiến SJC nới biên độ mua-bán để phòng rủi ro.
- **Sự kiện ảnh hưởng rõ nhất**: tháng 5/2024 giá tăng nóng lên đỉnh 92.2 triệu, sau đó **giảm ~12.6% trong tháng 6/2024** — đúng với thời điểm Ngân hàng Nhà nước can thiệp bán vàng bình ổn thị trường (sự kiện này không có trong danh sách EVENTS mặc định của script — bạn nên **bổ sung thủ công** vào danh sách `EVENTS` để phản ánh đúng, vì đây là sự kiện trong nước, không phải sự kiện quốc tế).
- **Mô hình dự đoán**: Linear Regression đạt RMSE ≈ 7.4 (đơn vị: triệu đồng/lượng). Nên thử thêm ARIMA (cần cài `statsmodels`) để so sánh — với dữ liệu có cú sốc giảm mạnh cuối kỳ (tháng 6/2024), ARIMA có thể bắt nhịp tốt hơn LR do mô hình hoá được tính tự tương quan ngắn hạn.
- **Phân cụm giai đoạn**: phần lớn thời gian được gán nhãn "Ổn định"; tháng 12/2023 và 5/2024 là "Tăng mạnh"; tháng 6/2024 là "Giảm/Điều chỉnh" — khớp với diễn biến thực tế.

## 6. Việc cần làm tiếp khi viết báo cáo/slide

1. **Bổ sung sự kiện trong nước** vào danh sách `EVENTS` trong code (đã thêm sẵn 3
   sự kiện: NHNN bán vàng bình ổn 06/2024, thế giới lập đỉnh 29/01/2026, điều chỉnh
   mạnh 06/2026 — bạn có thể bổ sung thêm).
2. Cài `statsmodels` và chạy lại để có kết quả ARIMA đầy đủ cho Câu hỏi 5.
3. Nêu rõ trong báo cáo: dữ liệu daily THẬT chỉ tới 06/2024; đoạn 07/2024 → nay là
   ước lượng tháng có nguồn (xem mục 2).
4. Nếu muốn dữ liệu DAILY đầy đủ cho cả giai đoạn 07/2024 → nay (thay vì ước lượng
   tháng), có thể tự thu thập thêm: trang **webgia.com** có công cụ "Tra cứu lịch sử
   giá vàng SJC" theo từng ngày cụ thể, ví dụ:
   `https://webgia.com/gia-vang/sjc/31-12-2025.html` (định dạng `DD-MM-YYYY.html`).
   Mở từng ngày cần, hoặc dùng Power Query trong Excel ("Get Data From Web") để tự
   động hoá việc này nếu cần độ chi tiết cao hơn 6 mốc hiện có.

