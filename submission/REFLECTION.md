Anti-pattern dễ mắc phải nhất trong Data Engineering thực tế là "Small-file problem" (vấn đề file quá nhỏ). 

Khi hệ thống stream dữ liệu liên tục, chúng ta thường ghi hàng ngàn file rất nhỏ vào data lake. Dù quá trình ghi (Write) diễn ra rất nhanh, nhưng khi cần phân tích (Read), Spark/Delta Lake sẽ phải tốn cực kỳ nhiều thời gian chỉ để quét metadata và đóng/mở hàng ngàn file nhỏ lẻ đó. Hậu quả là làm chậm nghiêm trọng tốc độ truy vấn (như trong bài lab Notebook 02, việc tìm kiếm ban đầu mất tới hơn 4 giây).

Giải pháp cho anti-pattern này là phải thiết lập lịch định kỳ chạy lệnh `OPTIMIZE` kết hợp với `ZORDER`. Hành động này sẽ gom các file nhỏ lại với nhau (compaction) và phân loại dữ liệu tối ưu, giúp tăng tốc độ đọc lên hàng chục lần (chỉ còn 0.36 giây).
