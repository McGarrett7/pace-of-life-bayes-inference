# The Pace of Life — Phân tích mối quan hệ giữa nhịp sống và bệnh tim

## 1. Bối cảnh

Dự án tái hiện nghiên cứu của R.V. Levine, *"The Pace of Life"*, American Scientist 78 (1990): 450–59.
Một số người tin rằng những cá nhân luôn có cảm giác gấp gáp về thời gian (hành vi type-A) dễ mắc bệnh tim
hơn những người sống thong thả. Các nhà tâm lý học đã khảo sát 36 thành phố của Mỹ, đo 3 chỉ số "nhịp sống"
và đối chiếu với tỷ lệ tử vong do bệnh tim đã hiệu chỉnh theo tuổi.

## 2. Dữ liệu

File: [`data/pace_of_life.csv`](data/pace_of_life.csv)

| Biến | Ý nghĩa |
|---|---|
| `City` | Tên thành phố |
| `Walk` | Tốc độ đi bộ của người đi đường trên quãng đường 20m (chỉ số càng cao = đi càng nhanh) |
| `Bank` | Thời gian trung bình nhân viên ngân hàng trả tiền thối cho 2 tờ $20 (chỉ số càng cao = xử lý càng **chậm**) |
| `Talk` | Tốc độ nói của nhân viên bưu điện (số âm tiết / thời gian phản hồi, càng cao = nói càng nhanh) |
| `Heart` | Tỷ lệ tử vong do bệnh tim đã hiệu chỉnh theo tuổi |

Lưu ý: các biến đã được chuẩn hoá về thang đo không đơn vị theo mô tả gốc của bộ dữ liệu.

**Hướng giải thích "nhịp sống gấp gáp" (time urgency) theo giả thuyết lý thuyết:** `Walk` cao, `Talk` cao,
`Bank` **thấp** (xử lý giao dịch nhanh). Lưu ý: kết quả phân tích thực tế cho thấy `Bank` **không tuân
theo** chiều giả định này trong dữ liệu — xem chi tiết trong `report/pace_of_life_report.md`.

## 3. Câu hỏi nghiên cứu

> Dữ liệu ủng hộ hay bác bỏ niềm tin rằng những người có cảm giác gấp gáp về thời gian dễ mắc bệnh tim hơn?

## 4. Cấu trúc project

```
Project/
├── data/
│   └── pace_of_life.csv          # Dữ liệu gốc
├── notebooks/
│   └── pace_of_life_analysis.ipynb   # Notebook phân tích đầy đủ
├── figures/                      # Biểu đồ xuất ra từ notebook
├── report/
│   └── pace_of_life_report.md    # Báo cáo kết quả & kết luận
└── README.md
```

## 5. Phương pháp phân tích

Báo cáo (`report/pace_of_life_report.md`) được tổ chức theo 4 phần: (i) mô tả dữ liệu, (ii) quá trình mô
hình hoá dữ liệu, (iii) quá trình ước lượng Bayes, (iv) đánh giá kết quả và biện luận trả lời câu hỏi
nghiên cứu. Notebook thực hiện các bước sau:

1. Thống kê mô tả (mean, độ lệch chuẩn, five-number summary, skewness, boxplot) cho từng biến.
2. Xây dựng chỉ số nhịp sống tổng hợp (Pace-of-Life composite index).
3. Phân tích tương quan Pearson giữa `Walk`, `Bank`, `Talk` và `Heart` (kèm kiểm định ý nghĩa thống kê, ma
   trận tương quan / heatmap).
4. Trực quan hoá bằng scatterplot, pairplot (matplotlib/seaborn).
5. **Ước lượng tần suất:** hồi quy tuyến tính bội `Heart ~ Walk + Bank + Talk` (statsmodels OLS), kiểm tra
   giả định hồi quy (VIF, Shapiro–Wilk, Breusch–Pagan, Cook's distance), và hồi quy đơn biến với chỉ số
   nhịp sống tổng hợp.
6. **Ước lượng Bayes:** đặt prior liên hợp Normal-Inverse-Gamma cho cùng mô hình hồi quy, suy ra hậu
   nghiệm dạng đóng, rồi cài đặt thuật toán **Gibbs sampling** (4 chain × 6000 vòng lặp) để lấy mẫu hậu
   nghiệm, chẩn đoán hội tụ bằng **Gelman–Rubin R-hat** và trace plot, tổng hợp khoảng tin cậy Bayes
   (credible interval) và xác suất hậu nghiệm `P(β>0|data)` cho từng hệ số.
7. Đối chiếu kết quả tần suất và Bayes, kết luận trả lời câu hỏi nghiên cứu và nêu giới hạn phân tích.

## 6. Cách chạy

```powershell
pip install pandas numpy scipy statsmodels matplotlib seaborn jupyter nbformat nbconvert ipykernel
jupyter nbconvert --to notebook --execute --inplace notebooks/pace_of_life_analysis.ipynb
```

Hoặc mở trực tiếp `notebooks/pace_of_life_analysis.ipynb` trong VSCode/Jupyter và chạy toàn bộ (Run All).

Kết quả (bảng số liệu, biểu đồ, kết luận) được trình bày chi tiết trong
[`report/pace_of_life_report.md`](report/pace_of_life_report.md).
