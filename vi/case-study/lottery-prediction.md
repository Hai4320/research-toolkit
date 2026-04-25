# Case Study — Một Project Dự Đoán Xổ Số (Đã Ẩn Danh)

> Project mà bộ toolkit này được rút ra. Chi tiết sản phẩm cụ thể đã được redact có chủ ý; chỉ phương pháp luận và bài học là công khai.

> **Lưu ý**: Source code và chi tiết sản phẩm của project gốc không công khai. Case study này chỉ chia sẻ **phương pháp luận và bài học rút ra** — không chia sẻ implementation, branding, hay design choice độc quyền.

## Bối cảnh

**Bài toán**: Một project nghiên cứu trong domain dự đoán độ ngẫu nhiên cao — cụ thể là một game xổ số mà người chơi chọn số và giải thưởng phụ thuộc vào việc match với kỳ quay. Mục đích ban đầu (ngầm): tìm chiến lược chọn số tốt hơn ngẫu nhiên.

**Đặc điểm domain**: Cực kỳ ngẫu nhiên (xổ số được engineer để gần i.i.d. có chủ đích). Đây là nơi **gambler's fallacy** mạnh nhất, và nơi motivated reasoning nguy hiểm nhất.

## Pha 1: Frame (đã làm thiếu sót ban đầu)

**Câu hỏi ban đầu (ngầm)**: "Có chiến lược chọn số nào tốt hơn ngẫu nhiên không?"

**Vấn đề**: Câu hỏi không pre-specify "tốt hơn theo metric nào" hay "khi nào tôi tuyên bố không tốt hơn".

→ **Bài học**: Charter (xem [templates/charter.md](../templates/charter.md)) quan trọng. Nếu không pre-commit, dễ HARK và optional-stop.

## Pha 2: Hypothesize (đã thử quá nhiều)

Team đã code và backtest **nhiều chiến lược**, gồm:

- Frequency-based (hot numbers — chọn theo tần suất xuất hiện)
- Long-absence (cold numbers / "due numbers" — chọn số lâu chưa ra)
- Pair-frequency (cặp số hay đi cùng nhau trong lịch sử)
- Pattern detection (recurring spacing trong chuỗi quay)
- Exponential decay (frequency với time-decay weighting)
- Bayesian-Dirichlet (posterior trên distribution các số)
- Max-entropy / stratified sampling
- Distance-based diversity
- Not-repeat (tránh các số mới quay)
- Wheeling systems
- Ensemble combinations
- Random baseline

**Vấn đề nhận ra (sau)**: Mỗi chiến lược thử = một lần gieo xúc xắc tìm noise. Với 10+ chiến lược và α=0.05, kỳ vọng có 0.5+ chiến lược "có ý nghĩa" thuần ngẫu nhiên.

→ **Bài học**: Phải pre-specify số chiến lược, áp dụng multiple-comparison correction. Xem [04-validation.md](../method/04-validation.md) §3.

## Pha 3: Validate (đã phát hiện nhiều bẫy)

Khi nghiêm túc backtest, các phát hiện:

### 3.1 Mọi chiến lược "frequency-based" đều là gambler's fallacy biến thể

- "Hot numbers" (số hay ra) → giả định non-stationarity hoặc bias vật lý không có bằng chứng.
- "Due numbers" (số lâu chưa ra) → giả định "phải ra để cân bằng" — sai cho i.i.d.
- "Pair frequency" → tìm pattern trong noise (Texas sharpshooter).

### 3.2 Backtest trên dữ liệu lịch sử cho kết quả đẹp giả

Nguyên nhân:
- **In-sample fitting**: chiến lược tinh chỉnh trên cùng dữ liệu test trên đó.
- **Multiple-comparison**: nhiều chiến lược, một số tự nhiên trông tốt.
- **Survivorship**: backtest trên giai đoạn cụ thể, không robust cross time.

### 3.3 Sanity check với shuffled data

Nếu xáo trộn ngẫu nhiên kết quả lịch sử và chạy lại các chiến lược → **kết quả tương tự với chiến lược ngẫu nhiên**. Đây là dấu hiệu mạnh: chiến lược không thực sự "thấy" pattern, chỉ exploit cấu trúc trong giai đoạn cụ thể.

→ **Bài học**: Permutation/shuffle test là defense mạnh, đáng làm với mọi predictive claim.

## Pha 4: Adversarial Review

Đã thêm agent `rigor-reviewer` (Claude-based) với mandate:

- Tìm gambler's fallacy và biến thể.
- Kiểm tra lookahead bias trong backtest.
- Đánh giá multiple-comparison correction.
- Cảnh báo nếu chiến lược thúc đẩy hành vi đánh bạc có hại.

Agent rigor-reviewer trong toolkit này ([agents/rigor-reviewer.md](../agents/rigor-reviewer.md)) là **phiên bản domain-agnostic** của agent đó — đã được generalize để dùng cho lĩnh vực bất kỳ.

→ **Bài học**: Adversarial reviewer (con người hoặc AI) là khoản đầu tư đắt giá nhất trong nghiên cứu. Không có nó, confirmation bias không bị chặn.

## Pha 5: Reframe (output thực sự có giá trị)

Sau khi chấp nhận: **không có edge dự đoán** (xổ số đúng là i.i.d.), project pivot theo 2 hướng:

### Hướng B — Coverage / Đa dạng

Engine gợi ý số chuyển từ "predict winning numbers" sang **"diversity-first ticket selection"**:

- Composite score nhẹ chỉ làm seed.
- Constraint: mỗi số xuất hiện tối đa một số lần bounded nhỏ across N tickets.
- Special numbers round-robin → coverage tối đa.
- Backtest đo **coverage và prize distribution**, không "expected ROI".

Kết quả: với một bundle vé, đạt coverage gần 100% không gian số chính — không tăng EV (vẫn negative-sum), nhưng tăng xác suất ít nhất 1 vé thắng giải nhỏ và làm trải nghiệm đa dạng hơn.

### Hướng C — Giải trí có ý thức

UI/UX rewrite với các nguyên tắc:

- Giao diện ngôn ngữ địa phương thân thiện (không phải English kỹ thuật).
- Bỏ jargon ("backtest", "ROI", "expected value" → cụm từ thường).
- Disclaimer rõ ràng: "Xổ số là ngẫu nhiên. Đây là giải trí, không phải kế hoạch tài chính. Chơi có trách nhiệm."
- Tính năng "Save / track" để user theo dõi gợi ý theo thời gian — biến project thành **companion để chơi có ý thức**, không phải tool dự đoán.

→ **Bài học**: Pivot **trung thực** thành entertainment có giá trị thật. Pivot **giả** (vẫn bán "có edge" với từ ngữ khác) là lừa dối.

## Bẫy đã đối mặt

Theo [03-fallacies.md](../method/03-fallacies.md):

- ✅ **Gambler's fallacy**: trực tiếp trong long-absence, frequency strategies.
- ✅ **Hot hand**: trong frequency-weighted strategies.
- ✅ **Texas sharpshooter**: pair_frequency, pattern strategies — tìm pattern sau khi nhìn dữ liệu.
- ✅ **P-hacking / multiple comparison**: nhiều chiến lược thử trên cùng dataset.
- ✅ **Confirmation bias**: ban đầu chú ý các backtest "đẹp" hơn các backtest fail.
- ✅ **Goodhart drift**: backtest metric (số lần thắng) trở thành mục tiêu, không phải mục tiêu thật (giải trí có ngân sách).

## Bài học đặc thù domain

Bổ sung vào fallacy list cho domain xổ số / random-number:

- **"Patterns trong dãy ngẫu nhiên trông giống pattern thật"**: não người tìm pattern kể cả khi không có. Mỗi pattern bạn "thấy" cần test out-of-sample.
- **"Backtest improvement không sống sót"**: edge nhỏ trong backtest thường biến mất khi tính chi phí vé, thuế, behavioral adherence.
- **"Coverage strategy không phải edge"**: phải cẩn thận không nói/bán nó như một edge. Nó chỉ làm distribution rủi ro khác đi, không tăng EV.

## Pattern tái sử dụng từ project

- **Walk-forward backtest framework**: pattern dùng được cho bất kỳ predictive strategy.
- **Adversarial reviewer agent**: đã generalize thành [agents/rigor-reviewer.md](../agents/rigor-reviewer.md).
- **UX-first reframing pattern**: rewrite jargon → friendly language khi pivot từ "tool có edge" sang "tool giải trí có ý thức". Pattern này áp dụng được cho mọi product có positioning thay đổi.
- **Bộ toolkit này**.

## Nếu làm lại từ đầu

Với nhận thức bây giờ, sẽ làm khác:

1. **Pre-commit charter** trước khi code dòng đầu tiên — cam kết tiêu chí "tôi sẽ tuyên bố không có edge khi ..."
2. **Pre-specify số chiến lược** — số lượng cố định nhỏ, không exploration vô bờ.
3. **Train/val/test split** từ đầu, test set chỉ chạm 1 lần ở cuối.
4. **Permutation test** sớm — trước khi đầu tư công sức tinh chỉnh chiến lược.
5. **Pivot decision sớm hơn**: ngay sau pha validate đầu tiên không đạt, không kéo dài thêm.
6. **Output là honest entertainment tool từ đầu** — không phải pretend predictive tool rồi pivot.

Các "thất bại" tạo ra bộ toolkit này — đầu tư phương pháp không lãng phí.

## Liên hệ tới đời sống

Pattern "đầu tư vào giả thuyết yêu thích → backtest trông đẹp → reviewer tìm ra lỗi → pivot trung thực sang giá trị thật" áp dụng cho:

- **Đầu tư cá nhân**: tin có hệ thống → backtest đẹp → friction giết edge → pivot sang index investing.
- **Productivity hack**: tin morning routine X tối ưu → tracker cho thấy không tương quan → pivot sang "consistency, không phải optimization".
- **Diet experiment**: tin keto/IF/X giải quyết vấn đề → kết quả không robust → pivot sang "ăn ít junk + đủ ngủ".

Trong mọi trường hợp, **value trung thực sau pivot > value giả vờ trước pivot**.
