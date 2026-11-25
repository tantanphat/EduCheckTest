# EduCheck — Trợ lý tư duy phản biện cho sinh viên

EduCheck là ứng dụng web một trang (không cần backend) giúp sinh viên **kiểm tra nội dung do AI sinh ra** và học cách tư duy phản biện. Bạn dán một đoạn văn, ứng dụng tách thành từng luận điểm, đánh giá rủi ro, tìm nguồn tham khảo, rồi giúp bạn **sửa bài, luyện tập và lập danh mục tài liệu tham khảo**.

> Công cụ chỉ **hỗ trợ** tự rà soát. Kết quả do AI và heuristic tạo ra có thể sai. Luôn đọc nguồn gốc trước khi trích dẫn hoặc nộp bài.

---

## 1. Hai phiên bản

| File | Mô tả |
|---|---|
| `index.html` | **Ver 1** — bản đơn giản: một lần gọi Gemini, trả trạng thái từng câu (đáng tin / cần xác minh / sai lệch). |
| `index-2.html` | **Ver 2** — bản đầy đủ, mô tả trong tài liệu này. Khuyến nghị dùng bản này. |

Hai bản độc lập, không dùng chung code.

## 2. Tính năng của Ver 2

**Phân tích**
- Tách văn bản thành luận điểm (`Intl.Segmenter`), phân loại: số liệu, ngày/sự kiện, trích dẫn, nhân quả, tuyệt đối, ý kiến; tính điểm "đáng kiểm chứng".
- **Hội đồng Gemini** đánh giá từng luận điểm: chất lượng bằng chứng, phản biện, điểm rủi ro 0–100, khuyến nghị, gợi ý viết lại, câu hỏi Socratic, ngụy biện.
- **Xác minh DOI trên Crossref**: phát hiện tài liệu tham khảo bịa.
- **Heuristic cục bộ** (không tốn API): dấu hiệu ảo giác (số liệu không nguồn, số quá chính xác, "nghiên cứu cho thấy" không dẫn nguồn, từ tuyệt đối), 7 loại ngụy biện, thống kê văn phong.
- **Khả năng do AI viết** (chỉ tham khảo): kết hợp độ biến thiên độ dài câu, độ đa dạng từ vựng, cụm từ nối rập khuôn, lặp cụm và đánh giá của Gemini.
- Điểm tin cậy tổng thể, nhận định chung, tóm tắt, luận điểm chính, điểm mạnh/yếu.

**Học tập**
- **Danh mục tài liệu tham khảo** từ các DOI trong văn bản đã được Crossref xác minh: xuất APA 7, IEEE, BibTeX; DOI có link bấm được.
- **Quiz tự sinh** từ chính các lỗi trong văn bản của bạn (trắc nghiệm, có giải thích).
- **Viết lại có chú thích nguồn**: làm nhẹ/bỏ khẳng định thiếu căn cứ, chỉ trích dẫn các DOI đã xác minh trong văn bản; có bản xem trước với link bấm được.

**Tiện ích**
- Chuyển ngôn ngữ giao diện **EN/VI** (kể cả ngôn ngữ Gemini trả lời).
- Sáng/tối, responsive (drawer trên mobile), lịch sử phiên (tối đa 40, lưu trong trình duyệt), phím tắt `Ctrl/⌘ + Enter`.
- Xuất PDF. Lọc kết quả theo mức rủi ro.

## 3. Bắt đầu nhanh

Không cần cài đặt hay build.

1. Lấy Gemini API key miễn phí tại <https://aistudio.google.com/app/apikey>.
2. Mở `index-2.html` bằng trình duyệt hiện đại (Chrome, Edge, Firefox, Safari mới). Cần Internet để tải Tailwind, font và gọi API.
3. Bấm **AI Panel**, dán API key.
4. Dán văn bản (tối đa 10.000 ký tự) và bấm **Phân tích với EduCheck** (hoặc `Ctrl + Enter`).

Có thể chạy qua server tĩnh nếu thích:

```bash
python3 -m http.server 8000
# mở http://localhost:8000/index-2.html
```

## 4. Hướng dẫn sử dụng

### Tùy chọn phân tích (panel bên phải, trước khi chạy)

| Tùy chọn | Tác dụng | Chi phí |
|---|---|---|
| Xác minh DOI trên Crossref | DOI không tồn tại → +25 điểm rủi ro. DOI có thật → đưa metadata vào prompt. | Chỉ request mạng tới Crossref |
| Chế độ nghiêm ngặt | Hạ ngưỡng (thấp < 25, trung bình < 55) và cộng 8 điểm cho mọi luận điểm. | Không tốn thêm |

### Đọc kết quả
- Văn bản gốc được tô màu theo luận điểm; bấm vào một đoạn hoặc thẻ để đối chiếu.
- Thẻ luận điểm: mức rủi ro, loại, khuyến nghị; "Xem chi tiết" hiện bằng chứng, phản biện, ngụy biện, gợi ý viết lại, câu hỏi Socratic.
- Bộ lọc: Tất cả / Rủi ro cao / Cần xem lại / Rủi ro thấp / Đáng kiểm chứng.

### Lập danh mục tài liệu tham khảo
1. Đảm bảo tùy chọn **Xác minh DOI trên Crossref** đang bật khi phân tích.
2. Bấm **Tài liệu TK** → chọn APA / IEEE / BibTeX.
3. **Sao chép** hoặc **Tải về** (`references.txt` / `references.bib`).

Danh mục chỉ gồm các DOI có trong văn bản của bạn và đã được Crossref xác nhận tồn tại. Nếu văn bản không có DOI nào, danh mục sẽ trống.

### Viết lại có chú thích nguồn
Bấm **Viết lại** → Gemini viết lại toàn bộ văn bản. Mọi `[n]` được kiểm tra lại trong code (số không khớp nguồn nào bị xóa) và đánh số lại theo thứ tự xuất hiện; danh sách IEEE có link được thêm vào cuối. `[cần nguồn]` / `[citation needed]` đánh dấu chỗ chưa có nguồn phù hợp. Có thể sửa tay, sao chép, tải `.md` hoặc đưa vào ô soạn thảo.

> Nguồn được dùng chỉ là các DOI đã xác minh trong văn bản của bạn; Gemini có thể gắn sai nguồn cho câu. Hãy mở và đọc từng bài.

## 5. Cách hoạt động

```
Văn bản ─► 1 Cấu trúc ─► 2 Luận điểm ─► 3 Trích dẫn ─► 4 Kiểm tra nội bộ ─► 5 AI ─► 6 Tổng hợp
           (thống kê,     (tách câu,      (DOI/URL/       (ngụy biện,          (Gemini   (điểm rủi ro,
            văn phong)     phân loại)      tác giả-năm,    ảo giác)             theo lô   điểm tin cậy)
                                           Crossref)                            8 câu)
```

**Điểm rủi ro mỗi luận điểm** = điểm Gemini (0–100, mặc định 50 nếu thiếu) + 6 × số dấu hiệu ảo giác + 4 × số ngụy biện cục bộ + 25 (DOI không tồn tại) + 4 (trích dẫn không có DOI), cộng chế độ nghiêm ngặt (+8). Mức: thấp < 35, trung bình < 65, cao ≥ 65.

**Điểm tin cậy** = 100 − trung bình có trọng số của điểm rủi ro (trọng số = 1 + điểm đáng kiểm chứng / 50).

**Khả năng do AI viết** = 0,4 × điểm văn phong cục bộ + 0,6 × đánh giá Gemini (chỉ dùng điểm cục bộ khi văn bản đủ ≥ 80 từ). Đây chỉ là chỉ báo — công cụ phát hiện AI có thể sai, **đừng dùng làm bằng chứng duy nhất**.

## 6. Công nghệ

- HTML5 + CSS3 + JavaScript ES Modules thuần, một file, không build.
- [Tailwind CSS](https://tailwindcss.com) (CDN), Google Fonts, Material Icons.
- [html2pdf.js](https://github.com/eKoopmans/html2pdf.js) 0.10.1 (xuất PDF).
- API: Google **Gemini** (structured output), **Crossref** (xác minh DOI, không cần key).
- Lưu trữ: `localStorage` (API key, theme, ngôn ngữ, tùy chọn, lịch sử phiên).

## 7. Quyền riêng tư và bảo mật

- Văn bản của bạn được gửi tới **Google Gemini**; DOI được gửi tới Crossref. Không có server của dự án.
- API key và lịch sử chỉ lưu trong trình duyệt này (`localStorage`), key được gửi tới Google qua header `x-goog-api-key`. **Không dùng máy dùng chung** và thu hồi key nếu bị lộ.
- Mọi nội dung đưa lên giao diện đều được escape (chống XSS). Văn bản người dùng được đặt trong khối dữ liệu và model được dặn không làm theo chỉ dẫn bên trong (giảm prompt injection, không loại trừ hoàn toàn).
- Xóa dữ liệu: **Cài đặt → Xóa toàn bộ phiên**, hoặc xóa dữ liệu trang trong trình duyệt.

## 8. Giới hạn đã biết

- Tối đa 10.000 ký tự đầu vào và 40 luận điểm mỗi lần phân tích.
- Đánh giá "đúng/sai" hoàn toàn dựa vào Gemini (không có bước đối chiếu web), nên có thể sai hoặc bịa. Hãy tự kiểm tra nguồn.
- Chỉ xác minh được DOI; trích dẫn dạng "Tác giả (năm)" không có DOI chỉ được cảnh báo.
- APA/IEEE xuất dạng văn bản thuần (không in nghiêng tên tạp chí).
- Tên model (`MODEL_NAME` trong file, mặc định `gemini-3.5-flash-lite`) cần khớp model bạn có quyền dùng; nếu lỗi, đổi hằng số này.
- Heuristic cục bộ dựa trên regex nên có thể báo nhầm hoặc bỏ sót, nhất là với văn bản ngắn.

## 9. Khắc phục sự cố

| Triệu chứng | Nguyên nhân thường gặp | Cách xử lý |
|---|---|---|
| `Invalid API key` | Key sai/hết hạn | Tạo key mới tại Google AI Studio |
| `AI batch … failed` / HTTP 429 | Hết quota hoặc quá tải | Chờ rồi thử lại; ứng dụng đã tự retry 1 lần |
| Danh mục TLTK trống | Văn bản không có DOI nào được Crossref xác minh (hoặc Crossref bị chặn) | Thêm DOI vào văn bản, bật tùy chọn Crossref |
| `AbortSignal object could not be cloned` | Chạy trong iframe/preview có sandbox | Đã được xử lý ở phiên bản hiện tại; nếu còn, mở file trực tiếp bằng trình duyệt |
| Giao diện không có kiểu | Không tải được Tailwind CDN | Kiểm tra Internet / chặn quảng cáo |

## 10. Cấu trúc thư mục

```
EduCheckTest/
├── index.html     # Ver 1 (đơn giản)
├── index-2.html   # Ver 2 (đầy đủ): giao diện + CSS + JS trong một file
└── README.md
```

Trong `index-2.html`, phần `<script type="module">` gồm các khối chính: `I18N DICTIONARY` (bảng dịch vi/en) → `GLOBAL STATE` → `SESSION STORAGE` → sidebar/lịch sử → `ANALYSIS ENGINE` (heuristic, Crossref, Gemini, `runPipeline`) → `RENDER RESULTS` → modal (Tài liệu TK, Quiz, Viết lại) → xuất file → khởi tạo.

## 11. Hướng phát triển

Chế độ luyện tập (tự đánh dấu luận điểm đáng ngờ rồi chấm điểm), sơ đồ cấu trúc lập luận, nhập từ URL/PDF, thống kê tiến bộ, test cho các hàm thuần.
