# Reflection — Anti-pattern Analysis

## Anti-pattern: Small-file Problem & Missing Schema Enforcement

Qua bài lab này, anti-pattern mà team dễ mắc nhất là **Small-file problem** kết hợp với **thiếu kiểm soát schema**.

**Small-file problem:** Trong Notebook 02, trước khi chạy OPTIMIZE, hệ thống tạo ra hàng trăm file nhỏ khiến việc truy vấn mất tới **4.42 giây**. Sau khi áp dụng `OPTIMIZE + ZORDER BY (user_id)`, tốc độ tăng lên **12.1×** (chỉ còn 0.36s). Trong thực tế, khi pipeline stream dữ liệu liên tục mà không có lịch compaction định kỳ, hiệu suất đọc sẽ suy giảm nghiêm trọng theo thời gian.

**Thiếu schema enforcement:** Notebook 01 cho thấy khi ghi dữ liệu sai kiểu (`age` là string thay vì integer), Delta Lake đã **chặn ngay lập tức** với lỗi `DELTA_FAILED_TO_MERGE_FIELDS`. Nếu không có cơ chế này, dữ liệu bẩn sẽ âm thầm xâm nhập vào data lake — đúng như Notebook 04 minh họa: tầng Silver phải loại bỏ tới **50,019 bản ghi trùng lặp** từ 1,000,000 dòng Bronze.

Bài học rút ra: luôn bật schema enforcement, thiết lập lịch OPTIMIZE định kỳ, và xây dựng pipeline Medallion (Bronze → Silver → Gold) để kiểm soát chất lượng dữ liệu từng bước.
