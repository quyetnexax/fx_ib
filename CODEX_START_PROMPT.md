# Prompt khởi động Codex — Copy nguyên văn

Bạn đang tiếp nhận project **Forex_IB**.

Hãy **đọc theo thứ tự** `README.md`, `PROJECT_CONTEXT.md`, `AGENTS.md`, `BACKLOG.md` trong thư mục handoff, rồi kiểm tra repository hiện tại. Chúng ta đang xây nền tảng phân tích kỹ thuật XAUUSD (nguồn MT5 Exness) và phát hành nội dung đa kênh; mục tiêu ưu tiên trước mắt là Gold AI Analyst Bot gửi Telegram mỗi giờ, **chỉ phân tích xu hướng và các vùng hỗ trợ/kháng cự, không đặt lệnh, không phát tín hiệu BUY/SELL**.

Một cuộc trò chuyện trước đây đã báo cáo tạo `Gold_AI_Analyst_MT5_Telegram_MVP.zip` và 7/7 tests offline, nhưng bản ZIP không kèm handoff. **Không mặc định ZIP tồn tại hoặc tests đang pass**. Nếu repository có code cũ, tận dụng và bảo toàn. Nếu chưa có code, dựng tối thiểu project Python 3.11+ có modules riêng data source MT5, indicator/zone analysis, reporting, Telegram publisher, scheduler, persistence, fixtures và pytest; không đụng API live khi chưa có credentials.

**Nhiệm vụ đầu tiên trong phiên Codex:**
1. Liệt kê file/folder hiện có và đối chiếu với `PROJECT_CONTEXT.md`.
2. Chạy test có sẵn (nếu có) và báo kết quả thật.
3. Tạo `AUDIT_REPORT.md` ghi rõ đã có/chưa có/không xác minh.
4. Triển khai mục tiếp theo nhỏ nhất trong P1/P2 từ `BACKLOG.md`: chạy phân tích fixture -> tạo báo cáo tiếng Việt `--dry-run` -> pytest. Tránh viết mới hệ thống CMS/n8n khi MVP core chưa xác nhận chạy.
5. Trả lời có file nào đổi, lệnh chạy, test output, những điều chưa test được, bước tiếp theo.

**Quy tắc kỹ thuật:** chỉ dùng nến đã đóng; D1/H4 là bối cảnh H1 là báo cáo mỗi giờ; tính toán vùng giá bằng code, AI chỉ được diễn đạt lại JSON; UTC nội bộ và timezone Việt Nam khi hiển thị; idempotency theo cây nến H1; không gửi trùng; không post khi dữ liệu cũ/lỗi; không hard-code token. Khi xử lý hệ CMS + social sau này, Python phân tích, n8n điều phối, Payload CMS duyệt, Next.js trình bày, mỗi channel có adapter riêng.

Bắt đầu ngay bằng source audit và một thay đổi có thể chạy/test được. Đừng chỉ tạo proposal.