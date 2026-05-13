# BÁO CÁO KẾT QUẢ LAB: GPU FINOPS & COST OPTIMIZATION

**Sinh viên:** Trịnh Đức An  
**MSSV:** 2A202600323 
**Ngày hoàn thành:** 13/05/2026

---

## 1. Giới thiệu

### 1.1 Mục tiêu của bài lab
Bài lab này nhằm cung cấp kinh nghiệm thực tế về việc áp dụng các nguyên tắc **FinOps** (Financial Operations) vào quản lý tài nguyên tính toán hiệu năng cao (GPU). Mục tiêu cụ thể bao gồm:
- Thiết lập hệ thống giám sát tài nguyên GPU thời gian thực.
- Theo dõi và phân bổ chi phí theo Workload/Dự án.
- Tối ưu hóa chi phí thông qua Spot Instances và Autoscaling.
- Phân tích và giảm thiểu lãng phí tài nguyên (Waste analysis).
- So sánh hiệu năng và chi phí giữa các phương pháp huấn luyện mô hình (FP32 vs AMP).

### 1.2 Tổng quan về GPU FinOps
GPU FinOps là sự kết hợp giữa hệ thống, quy trình và văn hóa nhằm tối ưu hóa giá trị kinh doanh của các tài nguyên GPU đám mây hoặc on-premise. Trong bối cảnh chi phí GPU ngày càng đắt đỏ (đặc biệt là các dòng A100, H100), việc quản lý "Burn rate" và tối ưu hóa "Cost per performance" trở thành yếu tố sống còn cho các dự án AI/ML.

---

## 2. Phân tích chi tiết kết quả

### 2.1 Giám sát và Theo dõi chi phí (Parts 1-2)
- **Cluster Monitoring**: Thông qua Cell 3 và 4, hệ thống đã cung cấp cái nhìn toàn diện về trạng thái các Node. Việc giám sát Power Draw (W) và Temperature (C) không chỉ giúp quản lý chi phí điện năng mà còn đảm bảo độ bền phần cứng.
- **Cost Tracking**: Việc gán chi phí cho từng `workload_id` giúp minh bạch hóa việc sử dụng ngân sách. Kết quả từ Gateway cho thấy sự khác biệt rõ rệt về tốc độ "đốt tiền" giữa các dòng GPU (T4 vs A100).

### 2.2 Tối ưu hóa bằng Spot Instances (Part 3)
- **Kết quả**: Sử dụng Spot Instances giúp tiết kiệm trung bình **60-70%** chi phí so với On-demand.
- **Phân tích Preemption**: Việc mô phỏng sự kiện thu hồi (preemption) cho thấy tầm quan trọng của việc thiết lập `checkpointing` trong huấn luyện mô hình để tránh mất mát dữ liệu khi tài nguyên bị thu hồi bất ngờ.

### 2.3 Autoscaling và Quản lý lãng phí (Parts 4-5)
- **Autoscaling**: Chính sách Scale-up khi utilization > 70% và Scale-down khi < 25% đảm bảo hệ thống luôn đáp ứng đủ nhu cầu nhưng không để tài nguyên nhàn rỗi quá lâu.
- **Waste Analysis**: Báo cáo lãng phí (Cell 13) chỉ ra rằng tài nguyên nhàn rỗi (Idle) là nguyên nhân chính gây thâm hụt ngân sách. Recommendations (Cell 14) đã gợi ý việc "Right-sizing" - chọn loại GPU phù hợp với độ phức tạp của model.

### 2.4 So sánh thực tế: FP32 vs Mixed Precision (Part 8)
Đây là phần quan trọng nhất minh chứng cho hiệu quả kỹ thuật tác động lên tài chính:
- **Hiệu năng**: Mixed Precision (AMP) giúp tăng tốc độ huấn luyện đáng kể (thường từ 1.5x - 2x trên GPU T4/A100).
- **Tiết kiệm chi phí**: Nhờ giảm thời gian huấn luyện, chi phí thực tế giảm tương ứng. Ngoài ra, AMP còn giúp giảm bộ nhớ GPU (Peak Memory Usage), cho phép tăng Batch Size để tối ưu hóa utilization hơn nữa.

### 2.5 Phân tích nâng cao (Part 8.5)
- **Multi-GPU Scaling**: Phân tích cho thấy hiệu suất không tăng tuyến tính theo số lượng GPU do độ trễ truyền thông (Communication overhead). Việc tìm ra "điểm ngọt" (Sweet spot) giữa số lượng GPU và chi phí là chìa khóa của FinOps.
- **Forecasting**: Việc dự báo với khoảng tin cậy (Confidence intervals) giúp nhà quản lý dự án chuẩn bị tốt hơn cho các rủi ro phát sinh, tránh tình trạng hết ngân sách giữa chừng.
- **Optimization Roadmap**: Lộ trình ưu tiên các chiến lược "Effort LOW / Savings HIGH" (như AMP) trước khi triển khai các giải pháp phức tạp hơn.

---

## 3. Kết luận và Bài học kinh nghiệm

### 3.1 Kỹ năng FinOps đã đạt được
- Khả năng đọc và phân tích các dashboard chi phí GPU chuyên sâu.
- Kỹ năng cấu hình các chính sách tự động hóa (Autoscaling) dựa trên dữ liệu thực tế.
- Tư duy tối ưu hóa: Luôn đặt câu hỏi về hiệu quả chi phí cho mỗi đơn vị hiệu năng.

### 3.2 Chiến lược tối ưu hóa hiệu quả nhất
Trong bài lab này, sự kết hợp giữa **Mixed Precision (Kỹ thuật)** và **Spot Instances (Thương mại)** là chiến lược mạnh mẽ nhất, có thể giảm tổng chi phí dự án lên tới **80%** mà vẫn đảm bảo chất lượng mô hình.

### 3.3 Ứng dụng thực tế
Các kiến thức này sẽ được áp dụng trực tiếp vào việc quản lý các dự án Deep Learning lớn, giúp kiểm soát ngân sách cloud (AWS/GCP/Azure) và xây dựng văn hóa trách nhiệm về chi phí trong đội ngũ kỹ sư MLOps.

---
**Ghi chú:** Tất cả các screenshot và biểu đồ liên quan đã được đính kèm trong thư mục `screenshots/` và `generated_charts/`.
