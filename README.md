# DENSO---A2: Accumulate Master Know-How for AI to Learn
# Expert Trace --- Tích lũy tri thức chuyên gia

> Prototype minh họa cách chuyển nội dung Design Review từ video/âm
> thanh thành hồ sơ tri thức có bằng chứng, sau đó hỗ trợ tìm kiếm và
> tái sử dụng kinh nghiệm. Dữ liệu trong demo hiện tại là dữ liệu mô
> phỏng, chưa phải dữ liệu do BTC/doanh nghiệp cung cấp.

## 1. Mục tiêu đề án

Expert Trace hướng đến việc lưu giữ không chỉ kết quả cuối cùng của một
phiên Design Review mà còn cả bối cảnh, quan sát, phương án được cân
nhắc, lý do ra quyết định, rủi ro và những thông tin còn chưa xác minh.

Giá trị doanh nghiệp kỳ vọng: - Giảm phụ thuộc vào trí nhớ và sự sẵn có
của chuyên gia. - Hạn chế phân tích lại những vấn đề đã từng được thảo
luận. - Giúp người dùng truy ngược bằng chứng thay vì nhận câu trả lời
không rõ nguồn. - Chỉ đưa hồ sơ vào kho tri thức chính thức sau khi
chuyên gia xác nhận.

## 2. Cấu trúc file của prototype

``` text
Expert_Trace_Chatbot_Demo/
├── index.html          # Giao diện web chatbot, Knowledge Base và Trace Pipeline
├── README.md           # Tài liệu tổng quan, cách chạy, pipeline, schema và chi phí
└── data/
    ├── cases.js        # 6 hồ sơ demo ở dạng JavaScript để trình duyệt tải trực tiếp
    └── cases.json      # Cùng bộ dữ liệu ở dạng JSON dễ đọc/chỉnh sửa
```

### Vai trò từng file

-   `index.html`: giao diện chatbot; hiển thị câu trả lời, bằng chứng,
    giới hạn và các hồ sơ mẫu.
-   `data/cases.js`: dữ liệu demo được nạp trực tiếp bởi trang HTML.
-   `data/cases.json`: bản JSON thuận tiện để xem cấu trúc và làm đầu
    vào cho backend sau này.

**Lưu ý:** bản demo hiện là frontend chạy tại máy, dùng luật/từ khóa đơn
giản để chọn hồ sơ phù hợp. Đây chưa phải LLM thật và chưa triển khai
RAG thực sự. `cases.json` và `cases.js` cần được cập nhật đồng bộ nếu
sửa dữ liệu thủ công.

## 3. Cách chạy

1.  Giải nén gói demo.
2.  Mở `index.html` bằng Chrome hoặc Edge.
3.  Chọn câu hỏi gợi ý hoặc nhập câu hỏi vào ô chat.
4.  Chọn **Knowledge Base** để xem hồ sơ mẫu; chọn **Trace Pipeline** để
    xem các bước nghiệp vụ.

Không cần cài Python, không cần API key và không phát sinh phí API khi
chạy bản demo hiện tại.

## 4. Pipeline đề xuất cho hệ thống thực tế

``` text
Video/Audio Design Review + tài liệu bối cảnh
                    │
                    ▼
        1. Quản lý file gốc và nguồn dữ liệu
                    │
                    ▼
        2. Speech-to-Text (STT) + timestamps
                    │
                    ▼
        3. Làm rõ người nói và thuật ngữ
           (nếu dữ liệu hỗ trợ; đánh dấu phần không rõ)
                    │
                    ▼
        4. LLM trích xuất Decision Trace
           • Vấn đề và bối cảnh
           • Quan sát/sự kiện được nêu
           • Các phương án được cân nhắc
           • Lý do, rủi ro và quyết định
           • Câu hỏi còn mở/thông tin còn thiếu
                    │
                    ▼
        5. Gắn bằng chứng cho từng nhận định
           (file, timestamp, đoạn transcript/tài liệu)
                    │
                    ▼
        6. Chuyên gia kiểm tra, sửa, duyệt hoặc từ chối
                    │
                    ▼
        7. Knowledge Base + chỉ mục tìm kiếm/RAG
                    │
                    ▼
        8. Chatbot trả lời dựa trên hồ sơ liên quan,
           trích dẫn nguồn và nêu rõ giới hạn
                    │
                    ▼
        9. Ghi nhận phản hồi/kết quả áp dụng để cải tiến
```

### Nguyên tắc vận hành

-   Giữ nguyên file nguồn; bản làm sạch hoặc transcript là bản dẫn xuất.
-   Không tự biến giả thuyết thành nguyên nhân gốc rễ nếu chưa có bằng
    chứng.
-   Mỗi nhận định quan trọng cần liên kết được về nguồn.
-   Thông tin thiếu hoặc mâu thuẫn phải được đánh dấu để chuyên gia xử
    lý.
-   Chỉ hồ sơ được duyệt mới trở thành tri thức chính thức.
-   Triển khai thực tế cần đáp ứng chính sách bảo mật, phân quyền và lưu
    trữ nội bộ của doanh nghiệp.

## 5. Schema hồ sơ tri thức

Schema dưới đây là **đề xuất ban đầu cho prototype**, không phải schema
do BTC cung cấp hoặc tiêu chuẩn đã được doanh nghiệp phê duyệt.

``` json
{
  "case_id": "DR-001",
  "title": "Tín hiệu cảm biến vị trí không ổn định",
  "status": "demo_unreviewed",
  "problem": {
    "description": "Tín hiệu cảm biến vị trí không ổn định, chu kỳ dừng không đều.",
    "context": "Bối cảnh phiên Design Review; bổ sung theo dữ liệu thực tế."
  },
  "observations": [
    {
      "statement": "Tín hiệu lúc có lúc không.",
      "evidence_ids": ["EV-001"],
      "certainty": "reported"
    }
  ],
  "options_considered": [
    {
      "option": "Kiểm tra kết nối và vị trí gá trước khi thay linh kiện.",
      "status": "selected_for_investigation",
      "rationale": "Cần phân biệt các khả năng trước khi thay linh kiện.",
      "evidence_ids": ["EV-001"]
    },
    {
      "option": "Thay cảm biến ngay.",
      "status": "not_selected",
      "rationale": "Chưa có kết quả đo xác nhận."
    }
  ],
  "decision": {
    "statement": "Kiểm tra trạng thái tín hiệu, căn chỉnh và kết nối trước.",
    "decision_status": "proposed",
    "evidence_ids": ["EV-001"]
  },
  "root_cause": {
    "value": null,
    "status": "unconfirmed",
    "evidence_ids": []
  },
  "risks_and_limits": [
    "Chưa có sơ đồ điện, thông số đo hoặc hướng dẫn nhà sản xuất."
  ],
  "open_questions": [
    "Nguyên nhân gốc rễ cuối cùng là gì?",
    "Có kết quả kiểm tra trước/sau hay không?"
  ],
  "evidence": [
    {
      "evidence_id": "EV-001",
      "source_file": "transcript_demo_DR-001.txt",
      "start_time": "00:01:14",
      "end_time": "00:01:32",
      "quote": "Tín hiệu lúc có lúc không. Trước khi thay, kiểm tra lại giắc và vị trí gá xem có bị lệch không.",
      "source_type": "simulated_transcript"
    }
  ],
  "review": {
    "reviewer": null,
    "review_status": "not_reviewed",
    "reviewed_at": null,
    "notes": null
  }
}
```

### Ý nghĩa các trường quan trọng

-   `case_id`: mã hồ sơ duy nhất.
-   `problem` và `observations`: vấn đề/bối cảnh và những gì được ghi
    nhận.
-   `options_considered`: các phương án, trạng thái lựa chọn và lý do
    được ghi nhận.
-   `decision`: quyết định hoặc bước tiếp theo; cần phân biệt quyết định
    tạm thời với kết luận cuối.
-   `root_cause`: để `null` và đánh dấu `unconfirmed` nếu chưa có bằng
    chứng.
-   `evidence`: liên kết về file nguồn, mốc thời gian và trích đoạn.
-   `review`: trạng thái chuyên gia xác nhận.
-   `status`: phân biệt hồ sơ mô phỏng, đang xử lý, chờ duyệt và đã
    duyệt.

## 6. Kiến trúc model và công cụ

  -----------------------------------------------------------------------
  Thành phần              Vai trò                 Lựa chọn triển khai
  ----------------------- ----------------------- -----------------------
  STT                     Chuyển lời nói thành    So sánh dịch vụ STT
                          văn bản và mốc thời     hoặc mô hình chạy nội
                          gian                    bộ theo độ chính xác
                                                  tiếng Việt, tiếng ồn và
                                                  chi phí

  LLM                     Trích xuất trường tri   Chọn model theo chất
                          thức, chuẩn hóa thuật   lượng trên dữ liệu mẫu;
                          ngữ, soạn câu trả lời   dùng model nhỏ cho việc
                                                  đơn giản, model mạnh
                                                  hơn cho hồ sơ khó

  Retrieval/RAG           Tìm hồ sơ và đoạn bằng  Tìm kiếm từ khóa kết
                          chứng liên quan trước   hợp embedding/vector
                          khi trả lời             search; chỉ dùng RAG
                                                  khi có kho dữ liệu thực

  Knowledge Base          Lưu hồ sơ, bằng chứng,  CSDL quan hệ/tài liệu
                          trạng thái duyệt và     cùng kho file nội bộ
                          phiên bản               

  Workflow                Chuyên gia duyệt và     Giao diện duyệt đơn
                          quản lý trạng thái      giản; LangChain hoặc
                                                  LlamaIndex là tùy chọn,
                                                  không bắt buộc cho
                                                  prototype

  Triển khai              Bảo mật và vận hành     Ưu tiên môi trường được
                                                  doanh nghiệp phê duyệt;
                                                  cân nhắc on-premise nếu
                                                  dữ liệu nhạy cảm
  -----------------------------------------------------------------------

Không nên khóa lựa chọn model trước khi có dữ liệu BTC. Cần thử trên một
tập đại diện để so sánh độ chính xác trích xuất, khả năng bám bằng
chứng, tốc độ, bảo mật và tổng chi phí.

## 7. Mô hình chi phí Prototype (ước tính minh họa)

Các giá trị dưới đây là giả định để lập ngân sách ban đầu, không phải
báo giá chính thức. Có thể thay bằng báo giá và dữ liệu sử dụng thực tế.

  -----------------------------------------------------------------------
  Hạng mục                             Giả định                   Chi phí
  ------------------- ------------------------- -------------------------
  Audio đầu vào               10 giờ = 600 phút                       ---

  STT                 0,006 USD/phút × 600 phút                  3,60 USD

  LLM                  100 lượt × 0,01 USD/lượt                  1,00 USD

  Lưu trữ/hạ tầng thử                 Khoán gọn                 30,00 USD
  nghiệm                                        

  Phát triển                 10 ngày công × 100              1.000,00 USD
  prototype                            USD/ngày 

  Chuyên gia duyệt           8 giờ × 25 USD/giờ                200,00 USD

  **Tổng ước tính**                                      **1.234,60 USD**

  Hồ sơ được duyệt                     10 hồ sơ                       ---

  **Chi phí bình                  1.234,60 ÷ 10            **123,46 USD**
  quân/hồ sơ được                               
  duyệt**                                       
  -----------------------------------------------------------------------

Nếu tạm quy đổi theo 1 USD = 26.000 VNĐ, tổng ngân sách khoảng **32,1
triệu VNĐ**. Tỷ giá chỉ để tham khảo.

### Cách diễn giải chi phí

-   Chi phí STT và LLM trực tiếp trong ví dụ chỉ khoảng **4,60 USD**;
    phần lớn ngân sách là nhân lực phát triển và chuyên gia duyệt.
-   Chi phí 123,46 USD/hồ sơ đang phân bổ toàn bộ chi phí làm prototype
    cho 10 hồ sơ; không phải chi phí biên khi hệ thống đã vận hành ổn
    định.
-   Chưa bao gồm kính/camera/micro, GPU riêng, tích hợp ERP/MES/CMMS,
    thuế, chi phí bảo mật hoặc hạ tầng sản xuất.
-   Đơn giá STT/LLM thay đổi theo nhà cung cấp, model, độ dài
    transcript, số token đầu vào/đầu ra và cách gọi model. Cần đo thực
    tế trước khi chốt ngân sách.

### Công thức nên dùng khi có số liệu thực

-   `Chi phí STT = tổng số phút audio × đơn giá STT/phút`
-   `Chi phí LLM = tổng token đầu vào × đơn giá input + tổng token đầu ra × đơn giá output`
-   `Chi phí duyệt = số giờ chuyên gia × đơn giá/giờ`
-   `Tổng chi phí = STT + LLM + lưu trữ/hạ tầng + nhân lực + duyệt + chi phí khác`
-   `Chi phí/hồ sơ được duyệt = tổng chi phí / số hồ sơ được chuyên gia duyệt`

## 8. Chỉ số đánh giá thành công

Đề xuất đo bằng dữ liệu thử nghiệm thay vì cam kết trước một mức cải
thiện chưa được kiểm chứng: - Độ chính xác của các trường trích xuất:
vấn đề, phương án, lý do và quyết định. - Tỷ lệ nhận định có bằng chứng
nguồn hợp lệ. - Tỷ lệ câu trả lời truy xuất đúng hồ sơ/đoạn nguồn. - Tỷ
lệ thông tin chưa xác minh được đánh dấu đúng, không bị biến thành kết
luận. - Thời gian chuyên gia cần để rà soát và duyệt một hồ sơ. - Thời
gian tìm lại tri thức so với cách làm hiện tại. - Tỷ lệ người dùng đánh
giá câu trả lời hữu ích và có thể áp dụng.
