# PROJECT_CONTEXT — Forex_IB
**Cập nhật:** 2026-10-26  
**Nguồn:** Nội dung và quyết định trong các cuộc trao đổi của project Forex_IB ngày 2026-10-25 và báo cáo bàn giao gần đây.

> Đây là bản tổng hợp để tiếp tục trên Codex, **không khẳng định** những phần thiết kế đã được triển khai. Dấu mốc trạng thái: `CONFIRMED REQUIREMENT`, `PREVIOUSLY REPORTED`, `PROPOSED`, `NOT VERIFIED`, `NOT IMPLEMENTED`.

## 1. Tầm nhìn
Forex_IB phát triển từ bot phân tích vàng thành hệ thống quản lý nội dung tài chính đa kênh:
- Lấy dữ liệu **XAUUSD** qua **MT5 Exness**.
- Tự phân tích xu hướng đa khung D1/H4/H1 và các vùng hỗ trợ/kháng cự.
- Tạo báo cáo tiếng Việt; phát **Telegram mỗi giờ** khi có nến H1 đóng mới.
- Tạo, chỉnh sửa, duyệt, lên lịch và xuất bản nội dung lên Telegram, Facebook, Zalo OA và website.
- Theo dõi kết quả, lỗi, retry, thống kê vận hành.
- **Không** đặt lệnh tự động. Người dùng chốt phạm vi đầu tiên là **chỉ xu hướng và vùng giá**, không yêu cầu khuyến nghị BUY/SELL hoặc chiến lược lệnh.

## 2. Trạng thái lịch sử chính xác

| Phần | Trạng thái | Ghi chú |
|---|---|---|
| Mục tiêu Telegram báo cáo mỗi giờ | CONFIRMED REQUIREMENT | XAUUSD, MT5 Exness, phân tích xu hướng + vùng |
| Thiết kế Gold AI Analyst Bot | PREVIOUSLY REPORTED | Đã có thiết kế và câu trả lời trước |
| ZIP `Gold_AI_Analyst_MT5_Telegram_MVP.zip` | PREVIOUSLY REPORTED / NOT VERIFIED | Trước đó đã báo cáo tạo ZIP MVP; **không có ZIP trong gói bàn giao này** |
| Test offline 7/7 | PREVIOUSLY REPORTED / NOT RE-RUN | Không có log/CI để kiểm chứng tại đây |
| Kết nối MT5 Exness thật | NOT VERIFIED | Cần môi trường MT5 Windows với tài khoản hợp lệ |
| Gửi Telegram thật | NOT VERIFIED | Cần Bot Token / Chat ID hợp lệ |
| AI narration / OpenAI | PREVIOUSLY REPORTED | Tùy chọn, không bắt buộc cho bản báo cáo rule-based |
| Kiến trúc Python + FastAPI + PostgreSQL + Payload + n8n + Next.js | PROPOSED | Là định hướng mở rộng, không phải sản phẩm đã triển khai |
| Publishing Facebook/Zalo/Website | NOT IMPLEMENTED | Chưa xác nhận kết nối API/quyền app/website |
| n8n workflows + CMS approval + monitoring | PROPOSED | Chưa xác nhận chạy thật |

**Điểm cần đối chiếu khi nhận repo:** Một trao đổi về thiết kế mong muốn H1~500, H4~300, D1~250 nến; bản MVP được báo cáo lấy **330 nến đã đóng cho từng H1/H4/D1**. Nếu có mã nguồn cũ, hành vi hiện tại phải lấy theo mã/test, không giả định các cấu hình đề xuất đã có. Thống nhất lại config và số nến tối thiểu bằng test.

## 3. MVP Gold AI Analyst Bot — đã được báo cáo trước đó

### Công nghệ & module
- Python 3.11, MetaTrader5 Python package, Pandas/NumPy (theo hướng thiết kế).
- MT5 terminal đăng nhập tài khoản Exness trên Windows PC/VPS; xử lý symbol có suffix nếu broker dùng.
- Dữ liệu 3 khung **D1 / H4 / H1**, sử dụng **nến đã đóng**, không đọc nến đang chạy để kết luận.
- EMA20/EMA50/EMA200, ATR14, trend tăng/giảm/đi ngang; xác định các vùng swing support/resistance.
- Bối cảnh D1, cấu trúc H4, cập nhật H1.
- Narration theo rule bằng tiếng Việt, có thể dùng LLM để diễn đạt nhưng LLM **không được tự tạo con số / vùng giá**.
- Gửi Telegram qua Bot API; có SQLite chống gửi trùng; bỏ qua khi market đóng/chưa có nến mới.
- Chế độ CLI trước đây được báo cáo: `--dry-run`, `--once`, `--loop`.
- Env từng được nhắc tới: `MT5_SYMBOL`, `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`, `OPENAI_API_KEY` (tùy chọn), `OPENAI_MODEL` (từng ví dụ `gpt-5-mini`), `RUN_AFTER_MINUTE=3`.
- Lịch chạy đề xuất: đầu mỗi giờ, **sau phút 03** (để đợi nến H1 đóng/xuất hiện). Dùng Windows Task Scheduler hoặc vòng lặp ứng dụng, chỉ **một** scheduler hoạt động để tránh duplicate.

### Những gì chưa được chứng minh
- ZIP nguồn gốc không hiện diện trong workspace bản bàn giao.
- 7 test offline là **kết quả đã được báo cáo**, chưa chạy lại.
- Không có bằng chứng kết nối tài khoản Exness hoặc Bot Telegram thật.
- Không có bằng chứng chart image, monitoring, retry production hay API web đã chạy.

## 4. Kiến trúc mở rộng: Gold Media Platform

```text
Exness MT5 (Windows terminal)
    |
    v
Python Data Collector -> Deterministic Technical Analysis -> Structured JSON + Chart
    |                                |
    +--------------------------------+
                     |
                     v
        FastAPI analysis service
                     |
                     v
        PostgreSQL + S3-compatible assets
                     |
                     v
      Payload CMS (editorial workflow)
                     ^
                     |
         n8n (scheduler + orchestration)
                     |
             Channel adapters
       +-------------+-------------+--------------+
       |             |             |              |
   Telegram       Facebook       Zalo OA      Next.js site
```

Các khối trên là **PROPOSED**, không phải tất cả đã lập trình.

### Nguyên tắc phân chia trách nhiệm
- **Python**: lấy OHLC/volume, chuẩn hóa thời gian, tính indikator/vùng, render chart, tạo bản phân tích xác định được.
- **FastAPI**: API nhận lệnh chạy phân tích/đọc bản phân tích, bảo mật; validate payloads.
- **PostgreSQL**: hệ dữ liệu lâu dài cho analysis, CMS, publication, attempts/audit (SQLite chỉ cho standalone MVP).
- **Payload CMS**: bài gốc, biến thể từng kênh, trạng thái soạn/duyệt/lịch đăng.
- **n8n**: lịch chạy, trigger, dispatch, retry/escalation/báo cáo. **Không** nhét logic EMA/ATR/SR vào n8n.
- **Next.js**: frontend website xem phân tích đã xuất bản.
- **Adapter kênh**: Telegram Bot API, Meta Graph API (nơi app có quyền, ví dụ Page), Zalo OA API (phụ thuộc chính sách/quyền OA), website; kiểm tra yêu cầu quyền và hạn mức trước khi triển khai.
- **Object storage**: ảnh chart, minh họa, thumbnail.

## 5. Luồng nội dung & xuất bản

### Workflow đề xuất
1. **WF-01 Analyze**: Mỗi giờ khi nến H1 đóng xong, n8n (hoặc scheduler độc lập giai đoạn MVP) trigger; phân tích -> JSON -> log -> kiểm tra chống trùng.
2. **WF-02 Compose**: Tạo tiêu đề, tóm tắt, bài phân tích, hashtag và ảnh chart (nếu có); số liệu chỉ từ structured analysis.
3. **WF-03 Editorial review**: Draft -> Review -> Approved -> Scheduled -> Published; có Rejected/Failed theo tình huống.
4. **WF-04 Publish**: Nhiều adapter độc lập; Telegram H1 mỗi giờ, website cập nhật phân tích, Facebook ~1–3 bài/ngày, Zalo theo lịch/quota — không spam theo giờ.
5. **WF-05 Retry/recover**: idempotency per (content, channel, schedule), exponential backoff/limit, dead-letter, hiển thị lỗi.
6. **WF-06 Daily ops**: Tóm tắt số lần phân tích, số bài draft/approved/published, lỗi, tỷ lệ thành công.

### Trạng thái nên tách
- Content workflow: `DRAFT`, `IN_REVIEW`, `APPROVED`, `SCHEDULED`, `ARCHIVED`.
- Publication cho từng channel: `PENDING`, `QUEUED`, `PUBLISHING`, `PUBLISHED`, `FAILED`, `SKIPPED`, `CANCELLED`.
- Không coi bài Facebook đăng thành công nghĩa là Zalo hoặc website thành công.
- Lúc đầu mọi kênh công khai trừ Telegram rule-based nên có **approval gate**, có thể cấu hình website auto-publish sau khi chất lượng ổn định.

## 6. Định dạng dữ liệu phân tích (PROPOSED contract)

Dữ liệu do engine tính ra. Không dùng ví dụ số như giá thật:

```json
{
  "symbol": "XAUUSD",
  "source": "mt5_exness",
  "as_of_utc": "ISO-8601 timestamp",
  "last_closed_h1_utc": "ISO-8601 timestamp",
  "timeframes": {
    "D1": {"trend": "up|down|sideways", "ema20": null, "ema50": null, "ema200": null, "atr14": null},
    "H4": {"trend": "up|down|sideways", "ema20": null, "ema50": null, "ema200": null, "atr14": null},
    "H1": {"trend": "up|down|sideways", "ema20": null, "ema50": null, "ema200": null, "atr14": null}
  },
  "zones": [
    {"kind": "support|resistance", "lower": null, "upper": null, "timeframe": "H4", "reason": "swing-cluster"}
  ],
  "summary_vi": "Nội dung chỉ dựa trên số liệu đã tính",
  "quality": {"valid": true, "issues": []},
  "chart_asset_key": null
}
```

Bản hợp đồng trên là ví dụ thiết kế, **không khẳng định API hiện tại đã trả đúng schema**. Nên dùng decimal/precision hợp lý cho dữ liệu giá, timestamp UTC với timezone rõ ràng.

## 7. Mô hình dữ liệu cần khi scale (PROPOSED)
- `analysis_runs`: id, symbol, timeframe anchor candle, created time, algo/config version, quality status, snapshot JSON.
- `price_zones`: analysis_id, support/resistance, lower, upper, timeframe, reason, confidence nếu có công thức tính, created_at.
- `content_items`: parent analysis, master copy, title, status, creator/approver, scheduled_at.
- `channel_variants`: content_id, channel, caption/body, media assets, preview.
- `publication_jobs`: variant_id, channel account, due_at, status, idempotency_key.
- `publication_attempts`: job_id, attempt, HTTP/API response code, external post ID, error, timestamps.
- `channel_accounts`: provider, authorized entity, safe credential reference (không lưu token thô vào logs).
- `audit_logs`: actor, action, before/after, time; bảo vệ khỏi lộ dữ liệu cá nhân/secret.

## 8. Chính sách chất lượng, an toàn và vận hành
- Chỉ tính trên nến **đã đóng**; cố định UTC trong DB, hiển thị `Asia/Ho_Chi_Minh`.
- Không phân tích giả khi MT5 disconnect, thiếu dữ liệu, giá bất thường hoặc thị trường nghỉ.
- Không tạo giá, vùng hoặc chart số liệu bằng prompt trống: **Python là nguồn sự thật**.
- Retry cho Telegram/social có idempotency, backoff, báo lỗi; không tạo bài trùng.
- Lưu symbol gốc theo broker, broker time, snapshot data và time của cây nến cuối cùng.
- Cảnh báo nếu hệ thống bỏ lỡ quá 1 chu kỳ hoặc không publish sau thời hạn.
- Không hard-code MT5 credentials, Bot Token, OpenAI key, social tokens.
- Giữ chính sách và hạn mức API Meta/Zalo; không dùng automation ngoài kênh được phép.
- Văn bản công khai phải có nhãn “Thông tin tham khảo, không phải khuyến nghị đầu tư”.
- Không giao dịch tự động, không gợi ý chốt lệnh khi chưa được yêu cầu.

## 9. Lộ trình đã đề xuất
Ước tính trước đó: **6 tuần, 2 dev** (chỉ là plan, phụ thuộc quyền API và chất lượng tích hợp).

1. Tuần 1: audit ZIP/source, setup repo, MT5 collector, dữ liệu và offline fixtures.
2. Tuần 2: hoàn thiện analysis + text + chart + Telegram, thử live, lịch H1/monitoring.
3. Tuần 3: FastAPI, PostgreSQL, entity analysis/content, n8n initial workflows.
4. Tuần 4: Payload CMS, draft/review/approve/schedule, Next.js website.
5. Tuần 5: Facebook Page / Zalo OA adapter với quyền API, preview và approval, retry.
6. Tuần 6: vận hành, monitoring, backup/recovery, quality/security checks, báo cáo.

Thứ tự ưu tiên: **MVP1 Gold Analyst Bot -> MVP2 Gold Media Platform**.

## 10. Quyết định nên giữ nguyên khi bắt đầu với Codex
1. XAUUSD + **MT5 Exness** là nguồn dữ liệu, không tự chuyển sang TradingView hoặc data provider khác.
2. **H1 mỗi giờ**, D1/H4 làm bối cảnh; chỉ chạy với nến đóng và chống trùng.
3. Nội dung chính là **xu hướng + vùng giá**, không phải bot dự đoán hoặc vào lệnh.
4. Kênh đầu tiên là **Telegram**, các kênh khác triển khai sau khi ổn định.
5. n8n phục vụ orchestration, **không tính toán phân tích kỹ thuật**.
6. Tách data/analysis, content, CMS, publishing và monitoring rõ ràng.
7. Phải audit code nguồn cũ trước khi sửa; nếu thiếu source, đánh dấu không có và dựng lại theo spec chứ không tuyên bố đã tích hợp.
8. Với token thật: chỉ cấu hình trong secret manager/.env ngoài Git.

## 11. Câu hỏi cần chốt sau khi audit repo, không được ngầm giả định
- Repo hiện có ZIP MVP cũ hay không, đã kết nối tài khoản MT5/demo/live nào?
- Symbol chính xác ở Exness là `XAUUSD` hay tên có hậu tố?
- Engine H1/H4/D1 dùng 330 nến đồng nhất hay config riêng; định nghĩa vùng giá cụ thể và ngưỡng ATR?
- Telegram là group, channel hay chat cá nhân; Bot có quyền post không?
- Facebook Page và Zalo OA đã có quyền API phù hợp chưa?
- Website có domain, CMS instance và hạ tầng triển khai chưa?
- Phần trích xuất chart PNG có tồn tại trong ZIP cũ không?
- Ai có quyền duyệt/publish thủ công?

Không để những câu hỏi này chặn việc xây dựng fixtures, tests, kiến trúc module và chế độ dry-run.

## 12. Definition of Done theo lớp
**MVP1**: lấy dữ liệu xác thực; loại bỏ nến chưa đóng; báo xu hướng và vùng, có timestamp/source; gửi 1 tin / H1 mới; dry-run; logs; không gửi khi không có nến mới/mất kết nối; offline tests & live smoke test có chứng cứ.

**MVP2**: phân tích lưu vào PostgreSQL; bài có workflow duyệt; CMS + website chạy; từng adapter publish có retry/idempotency; có quyền kênh hợp lệ; dashboard trạng thái và báo cáo ngày; auth/log audit/backup.

## 13. Cách báo cáo kết quả triển khai cho người dùng
Cuối mỗi task: ghi `(1) thay đổi file nào (2) chức năng chạy được (3) lệnh test và kết quả thật (4) cần credentials/quyền gì để test live (5) rủi ro/vấn đề còn mở`. Không đánh dấu DONE chỉ vì đã viết code hoặc tạo docs.