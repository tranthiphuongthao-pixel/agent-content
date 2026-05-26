# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Mô tả dự án (Đề bài)

Xây dựng **Claude Code Skills** để tự động kiểm tra chất lượng bài viết SEO theo checklist branding của khách hàng.

Dự án gồm 2 skills:

### 1. `/seo-content-review` — Kiểm tra bài viết theo checklist branding
Đọc danh sách bài viết từ Google Sheet (trạng thái "Y/c duyệt"), đọc nội dung từng bài trên Google Docs, dùng Claude CLI chấm điểm theo 17 tiêu chí branding, ghi kết quả ✅/❌ vào output sheet.

```
/seo-content-review <link_google_sheet_danh_sach>
```

### 2. `/branding-check` — Báo cáo branding tổng quan cho cả dự án
Kiểm tra toàn bộ bài viết của một dự án, phân loại kết quả theo tiêu chí, ghi vào tab "Báo cáo Branding" trong Master Sheet.

```
/branding-check <link_google_sheet_danh_sach> <tên_dự_án>
```

---

## Cấu trúc thư mục

```
.claude/
├── agents/
│   ├── seo-content-review/   ← skill chính (có skill.py)
│   └── branding-check/       ← skill chính (có skill.py)
└── skills/
    ├── seo-content-review/   ← SKILL.md định nghĩa skill
    └── branding-check/       ← SKILL.md định nghĩa skill
```

## Cài đặt

```bash
pip install google-api-python-client google-auth python-dotenv anthropic
```

Tạo file `.env` từ template:
```bash
cp .claude/agents/seo-content-review/.env.example .claude/agents/seo-content-review/.env
```

Cần có 3 biến môi trường:
- `GOOGLE_CLIENT_ID`
- `GOOGLE_CLIENT_SECRET`
- `GOOGLE_REFRESH_TOKEN`

Lấy refresh token tại: https://developers.google.com/oauthplayground/ (scope: spreadsheets + documents.readonly)

---

## Luồng hoạt động chung

```
Google Sheet (input)
  └─ Lọc bài theo trạng thái
  └─ Lấy link Google Doc từng bài

Claude CLI (claude -p)
  └─ Đọc toàn bộ nội dung bài
  └─ Chấm từng tiêu chí checklist branding
  └─ Trả JSON: ket_qua = ĐẠT | CẦN CẢI THIỆN | CHƯA ĐẠT | Không đánh giá được qua text

Google Sheet (output)
  └─ Ghi kết quả ✅/❌ + ghi chú lỗi chi tiết
```

---

## 17 tiêu chí checklist branding (VNPAY)

| # | Nhóm | Tiêu chí |
|---|------|----------|
| 1 | Salience | Chính tả VNPAY (không viết VnPay, VNPay) |
| 2 | Salience | Chính tả số/tiền (1,3m; 02 ngày; 01 tỷ); không dùng thương hiệu khác; "quét mã QR" không "chuyển khoản" |
| 3 | Salience | Từ dùng đúng (không "Top", không "&", không viết hoa giữa bài) |
| 4 | Salience | Trình bày nội dung: "Nội dung chính của bài viết" |
| 5-6 | Salience | Hình ảnh đúng brand guide (không đánh giá được qua text) |
| 7 | Salience | Không truyền thông "miễn phí 3 năm" → chỉ "miễn phí hết 2028" |
| 8 | Meaning | Thể hiện rõ tính năng SP/DV |
| 9 | Meaning | Tone of voice: chuyên nghiệp, tự tin, đơn giản |
| 10 | Meaning | Writing style: ngắn gọn, súc tích, không hù dọa |
| 11 | Meaning | CTA không nhấn mạnh "nộp thuế trực tuyến" |
| 12 | Response | Am hiểu ngành + trích nguồn uy tín + disclaimer in nghiêng cuối bài |
| 13 | Response | Đồng cảm thấu hiểu + trích dẫn văn bản luật |
| 14 | Response | Bảng ≤4 cột, đoạn văn ≤5 dòng, ≤5 bullet liên tiếp |
| 15 | Response | Internal link + anchortext phân bổ toàn bài |
| 16 | Resonance | Nội dung bám sát hành trình khách hàng |
| 17 | Resonance | CTA + disclaimer cuối bài |
