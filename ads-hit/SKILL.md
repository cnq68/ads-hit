---
name: ads-hit
description: Lên ads Meta mới cho Goodnight (tài khoản Goodnight Bedding 1) theo naming convention của team và cấu trúc Andromeda. Lấy video + content từ tab Output, tên kênh + ID video từ tab Video List, ghép tên campaign/ad set/ad + UTM theo tab Campaign Name, cho duyệt rồi mới tạo trên Meta, xong tick cột "Đã lên ads". Dùng khi user nói "lên ads mới", "lên ads", "tạo ads", "chạy ads mới", hoặc /ads-hit.
---

# ads-hit: lên ads Meta Goodnight

Mọi bước tạo trên Meta chỉ làm SAU KHI user duyệt bảng tóm tắt ở Bước 4.

## Thông số cố định

| Mục | Giá trị |
|---|---|
| Tài khoản | VN: Vua Nệm - Goodnight Bedding 1, `453602867563242` (VND) |
| Page | Goodnight - Sleep Zone, `532762029929073` |
| Dataset/Pixel | VN - Online DTC Product Sales, `1309344807400249` |
| Sheet gốc | `1evCgZCQXSr8FOEgJoLGMYk9adOl_NZhbLGCNxJ-jWmQ` |
| UTM (url_tags) | lấy từ cột Q tab `Campaign Name` (xem Bước 4) |

Các tab dùng:
- `Naming Convention`: option của từng trường đặt tên.
- `Video List`: tên kênh + ID video.
- `Output`: video và content để lên ads.
- `Campaign Name`: công thức ghép tên campaign / ad / UTM (xem Bước 3).

## Bước 1: Chọn video từ tab Output

Đọc `'Output'!A1:Z`. **Xác định cột theo tên header ở dòng 1, không theo chữ cái** (sheet hay bị thêm/xoá cột). Header hiện tại (18/11/2026):
A Ngày | B Model tạo | C Người tạo | D Kênh sử dụng | E Sản phẩm | F Phiên bản | G Hook | H Tỉ lệ | I Thời lượng | J Source dùng (hàng) | K Link video | L Loại video (TOMOBO) | M Primary text | N Description | O Headline | P CTA | Q FEEDBACK NGƯỜI | R NGƯỜI FEEDBACK | S GEMINI FEEDBACK | T **Content đã duyệt** (checkbox) | U ADS ID (sử dụng content này) | V **Đã lên ads** (checkbox).

1. Hỏi user **sản phẩm** và **funnel của campaign** (option cột Funnel tab Naming Convention: tofu/mofu/bofu) trước.
2. Lọc các dòng Output thoả **tất cả**:
   - Sản phẩm khớp.
   - `Content đã duyệt` = TRUE. Dòng chưa duyệt thì **không bao giờ** dùng, kể cả khi user chỉ định; báo user là content chưa được duyệt.
   - `Đã lên ads` chưa tick.
   - Có `Link video`.
   - `Loại video (TOMOBO)` khớp funnel: `TO`/`TOFU` ↔ tofu, `MO`/`MOFU` ↔ mofu, `BO`/`BOFU` ↔ bofu (không phân biệt hoa thường). Cột này trống thì không dùng, báo user cần điền.
3. Liệt kê các dòng đạt (Phiên bản + Hook + Loại video) để user chọn. Không có dòng nào đạt thì báo rõ lý do (chưa duyệt / sai funnel / đã lên ads / thiếu link) và dừng.
4. Mỗi dòng được chọn = 1 ad. Map vào creative (bắt buộc đủ, không để trống; trường nào trống trong sheet thì hỏi user):

   | Trường ads | Cột Output | Field creative |
   |---|---|---|
   | Video | Link video | `video_id` |
   | Primary text | Primary text | `message` |
   | Headline | Headline | `headline` |
   | Description | Description | `description` |
   | CTA | CTA | `call_to_action_type` |
   | Destination | landing page ở Bước 3 | `link_url` |

- CTA map sang Meta: "Mua sắm ngay (Shop Now)" → `SHOP_NOW`, "Tìm hiểu thêm" → `LEARN_MORE`, ... (lấy giá trị trong ngoặc, viết hoa, cách bằng `_`). Trống thì mặc định SHOP_NOW cho sale, LEARN_MORE cho awareness.
- Nếu content còn placeholder dạng `[link sản phẩm]`, `[Ưu đãi giá]`... thì hỏi user giá trị điền vào trước khi tạo.

## Bước 2: Lấy option + tra kênh/video

**Option** đọc `'Naming Convention'!A2:K`. Cột: Bộ phận | Funnel | Mục tiêu | Type | Product | Nguồn đăng | Tên kênh | Content Pillar | ID video | ID sản phẩm | PIC. Không tự thêm giá trị ngoài danh sách; nếu user cần giá trị không có, hỏi lại và nhắc user bổ sung vào sheet.

- **Product**: map từ cột Sản phẩm của Output sang option cột E (viết thường, bỏ dấu cách: `Goodnight Foxy` → `goodnightfoxy`). Không khớp thì hỏi user.
- **ID sản phẩm**: đi cặp theo hàng với Product ở cột J (ví dụ goodnightfoxy ↔ 7010), tự điền.
- **Tên kênh, ID video**: cột Tên kênh và ID video trong Naming Convention để trống, lấy từ `'Video List'!A:K` (Người tạo | Tên kênh | Class Sản phẩm | SKU | Color_Tone | ID video | ...):
  - Tên kênh = kênh nguồn chính trong cột "Source dùng (hàng)" của Output (kênh đầu tiên, thường ghi "mặt + VO"; ví dụ `r29 mebeo_review (mặt + VO); r26 ...` → `mebeo_review`). Số `rN` không phải số dòng trong Video List, tra theo tên kênh.
  - ID video = cột ID video của dòng Video List có Tên kênh đó (và Class Sản phẩm khớp sản phẩm). Nhiều dòng khớp hoặc không tìm thấy thì hỏi user.
  - Nguồn đăng (KOC/KOL/IH) gợi ý theo cột Nguồn của Video List, cho user xác nhận.

## Bước 3: Hỏi user các trường còn lại

Dùng AskUserQuestion (tối đa 4 câu/lần, mỗi câu 2–4 option, nhiều hơn thì gộp các option hay dùng và để "Other").

**Tầng campaign / ad set (hỏi 1 lần):**
1. Bộ phận, Mục tiêu (Funnel đã chọn ở Bước 1).
2. Ngân sách/ngày cho mỗi ad set (VND). Không tự đoán.
3. Ngày bắt đầu (mặc định: ngay khi bật), có ngày kết thúc không.
4. Link đích (landing page): lấy ở tab `Thông tin sản phẩm HIT` (cột B Sản phẩm, C Trạng thái, D Landing page). Không hỏi nếu tìm được link hợp lệ.
   - Map sản phẩm: tìm dòng có cột B chứa tên sản phẩm ở cột Sản phẩm của Output (không phân biệt hoa/thường, ví dụ `Goodnight Foxy` ↔ `Chăn lông hồ ly Goodnight Foxy`). Không khớp hoặc khớp nhiều dòng thì hỏi user.
   - Check trạng thái cột C: chỉ dùng khi là `On`. Nếu `Pending`/`Off`/trống thì báo user là landing đang không chạy và hỏi có muốn dùng link khác không, không tự dùng.
   - Cột D có thể chứa nhiều link dạng `[On] link` / `[Off] link`: chỉ lấy link `[On]`, bỏ link `[Off]`. Nhiều link `[On]` thì hỏi user chọn. Link không có nhãn thì coi theo trạng thái cột C.

**Tầng ad (cho từng video):** Type (video là mặc định), Nguồn đăng, Content Pillar, PIC (mặc định `Quang`, không cần hỏi; chỉ đổi khi user yêu cầu). Chỉ hỏi trường chưa suy ra được ở Bước 1–2.

## Bước 4: Ghép tên + bảng duyệt

Theo công thức tab `Campaign Name` (nối bằng `-`, giữ nguyên chữ hoa/thường như option). Tên kênh và Content Pillar trống thì bỏ khỏi chuỗi (không để `--`); các trường khác bắt buộc.

- **Tên chiến dịch / UTM campaign** (từng ad): `{bộ phận}-{funnel}-{mục tiêu}-{type}-{product}-{nguồn đăng}-{tên kênh}-{pillar}-{ID video}-{ID sản phẩm}-{PIC}`
- **Tên quảng cáo**: `{funnel}-{mục tiêu}-{type}-{product}-{nguồn đăng}-{tên kênh}-{pillar}-{ID sản phẩm}-{PIC}` (bỏ bộ phận và ID video).
- **url_tags** (gắn ở creative): lấy nguyên giá trị cột Q (`UTM campaign`) của tab `Campaign Name`, không tự ghép. Cách lấy: với mỗi ad, ghi các trường vào dòng trống đầu tiên của `Campaign Name` (dòng có cột C trống): C Bộ phận, D Funnel, E Mục tiêu, F Type, G Product, H Nguồn đăng, I Tên kênh, J Content Pillar, K ID video, L ID sản phẩm, M PIC, N MEDIA LINK (cột Link video của Output). Không ghi vào A, B, Q (cột công thức). Rồi đọc lại A (tên chiến dịch), B (tên quảng cáo), Q (url_tags) của dòng đó và dùng đúng các giá trị này; nếu khác với chuỗi tự ghép ở trên thì theo sheet.
- **Tên campaign** trên Meta: chuỗi như UTM nhưng chỉ gồm các trường giống nhau ở mọi ad trong campaign; trường khác nhau giữa các ad thì bỏ khỏi tên campaign.
- **Tên ad set**: theo format tên quảng cáo, cũng chỉ giữ các trường chung của các ad trong ad set.

Ví dụ 1 ad:
- Campaign / UTM: `g9-tofu-thruplay-video-goodnightfoxy-KOC-mebeo_review-review-7694291501723045128-7010-Vu`
- Ad: `tofu-thruplay-video-goodnightfoxy-KOC-mebeo_review-review-7010-Vu`

Hiện bảng: tên campaign, ad set, từng ad (kèm dòng Output nguồn), UTM đầy đủ (cột Q), objective/optimization, ngân sách, link, primary text/headline/description/CTA, loại video (TOMOBO). Chờ user OK mới tạo. Nếu user không duyệt hoặc đổi trường, sửa/xoá lại các dòng vừa ghi trong `Campaign Name`.

## Bước 5: Cấu trúc Andromeda (cố định)

- **ABO**: không đặt budget ở campaign, đặt `daily_budget` ở ad set.
- **Mỗi ad 1 creative**, các creative trong ad set phải khác nhau thật (định dạng / chân dung / góc tiếp cận). Nếu 2 video chỉ khác hook/thumbnail (ví dụ 2 phiên bản cùng source), nhắc user là Andromeda sẽ gộp làm một.
- **Targeting broad**: `geo_locations: {countries:["VN"]}`, 18–65+, mọi giới tính, không interest, không lookalike, không gửi `targeting_automation` (để Advantage+ Audience bật).
- **Loại trừ tệp đã mua**: tìm custom audience có tên chứa `Purchase`/`mua` (loại lookalike) bằng `ads_get_ad_account_custom_audiences`, đưa vào `excluded_custom_audiences`. Hỏi user xác nhận tệp. Nếu không có tệp nào, báo user và hỏi có muốn tạo tệp Purchase 180 ngày từ dataset không.
- **Placements**: thủ công, chỉ Facebook + Instagram ở Feed, Stories, Reels. Gửi trong `targeting`: `"publisher_platforms":["facebook","instagram"],"facebook_positions":["feed","story","facebook_reels"],"instagram_positions":["stream","story","reels"]`. Nếu sửa placement trên ad set nháp đã tạo, sửa cả `targeting` lẫn field `placement` cho khớp, không thì Ads Manager kẹt ở "Verifying your edits".
- **Advantage+ Creative: BẬT** (`advantage_plus_creative: true`).

Map Mục tiêu → objective / optimization_goal:

| Mục tiêu | Objective | optimization_goal | Ghi chú |
|---|---|---|---|
| conversionpurchase | OUTCOME_SALES | OFFSITE_CONVERSIONS | promoted_object `{"pixel_id":"1309344807400249","custom_event_type":"PURCHASE"}`, destination WEBSITE |
| conversionlead | OUTCOME_LEADS | OFFSITE_CONVERSIONS | promoted_object pixel + `LEAD`, destination WEBSITE |
| engagement | OUTCOME_ENGAGEMENT | POST_ENGAGEMENT | destination ON_POST |
| reach | OUTCOME_AWARENESS | REACH | |
| thruplay | OUTCOME_ENGAGEMENT | THRUPLAY | destination ON_VIDEO |
| location | hỏi lại user | | chưa chốt cách chạy |

Loại trừ tệp đã mua áp dụng cho mọi mục tiêu trừ khi user nói khác.

## Bước 6: Tạo trên Meta

Media: Link video ở Output là link Google Drive dạng `.../file/d/{FILE_ID}/view`. Thử upload bằng URL tải trực tiếp `https://drive.google.com/uc?export=download&id={FILE_ID}`; nếu Meta không lấy được thì tải file về scratchpad qua Drive connector rồi upload file local. Cần thumbnail cho video.

Thứ tự: upload media (`ads_creative_upload_media`, chờ video `ready` qua `ads_get_ad_videos`) → `ads_create_campaign` → `ads_create_ad_set` → `ads_create_creative` (có `page_id`, `instagram_user_id` của IG đi với page, `link_url` = landing page, `message`, `headline`, `description`, `call_to_action_type`, `url_tags`, `advantage_plus_creative: true`) → `ads_create_ad` với `{"creative_id": ...}`. Dùng `hide_ui: true` khi tạo nhiều object.

**Trạng thái mặc định PAUSED.** Tool đang ở draft mode: object được lưu nháp. Sau khi tạo, đọc lại `active_errors` và sửa lỗi chặn nếu có.

**Ads Manager không hiển thị nội dung ad nháp tạo qua API** (ô Primary text/Headline/Description/Destination trống dù API có đủ). Vì vậy sau khi tạo xong và hết lỗi, hỏi user có muốn "publish ở trạng thái PAUSED" không. Nếu đồng ý:
1. `ads_update_entity` campaign `{"status":"PAUSED"}` (đổi trạng thái trong bản nháp).
2. `ads_activate_entity` campaign với `publish_as_active: false` → publish ra object thật nhưng campaign PAUSED, không tiêu tiền.
3. Đọc lại live: campaign `PAUSED`, ad có `creative_id`; đọc creative bằng `ads_get_creatives` để xác nhận body/title/description/link_url/CTA/url_tags.

Không bao giờ publish ACTIVE trừ khi user nói rõ "bật"/"chạy".

## Bước 7: Ghi lại vào sheet

Với mỗi dòng Output đã tạo ad thành công: ghi ad ID vào cột `ADS ID (sử dụng content này)` (hiện là U; nếu đã có ID thì nối thêm bằng `, `) và tick cột `Đã lên ads` (hiện là V) = `TRUE`. Xác định cột theo header như Bước 1. Dòng nào tạo lỗi thì không tick.

Cuối cùng báo user: ID campaign/ad set/ad, tên từng cái, UTM, các dòng Output đã tick, và nhắc vào Ads Manager duyệt bản nháp rồi publish.
