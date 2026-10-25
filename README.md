# Forex_IB — Codex handoff pack
**Chốt nội dung:** 2026-10-26 (Asia/Ho_Chi_Minh)  
**Loại tài liệu:** Bàn giao bối cảnh và kế hoạch, **không phải source code hoàn chỉnh**.

## Bắt đầu ở đâu?
1. Đọc `PROJECT_CONTEXT.md` để hiểu những gì đã bàn, đã có, còn thiếu.
2. Đọc `AGENTS.md` để nắm ràng buộc khi Codex sửa code.
3. Đọc `BACKLOG.md` để triển khai theo thứ tự, có tiêu chí nghiệm thu.
4. Dán `CODEX_START_PROMPT.md` vào Codex sau khi mở repository.
5. Nếu có file MVP cũ `Gold_AI_Analyst_MT5_Telegram_MVP.zip`, giải nén vào workspace riêng, kiểm tra cấu trúc, test và merge dần; **không** tự nhận rằng ZIP đang có trong handoff này.

## Trạng thái thực tế
Một phiên trước đã *báo cáo* tạo ZIP MVP Python kết nối MT5 Exness, phân tích và gửi Telegram; báo cáo 7/7 test offline qua. Chưa xác minh độc lập source ZIP, chưa có bằng chứng kết nối MT5 Exness thực hoặc gửi Telegram thành công. Handoff pack này không chứa ZIP nguồn cũ, token hoặc thông tin đăng nhập.

## Mục tiêu cuối
Phân tích XAUUSD từ MT5 Exness, gửi Telegram mỗi giờ (nến H1 đã đóng), mở rộng nội dung lên web / Facebook / Zalo OA qua CMS + n8n với kiểm soát và nhật ký xuất bản.

**Không giao dịch tự động. Không đặt lệnh, không tự suy diễn mức giá.**