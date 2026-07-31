---
title: "Event 2"
date: 2026-07-25
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# Bài thu hoạch “FCAJ x Agentic AI Build Week: Show Up. Build. Pitch. WIN!"

### Mục Đích Của Sự Kiện

- Hackathon: Kết nối cộng đồng kỹ sư trẻ, sinh viên đam mê công nghệ để hiện thực hóa các ý tưởng về Agentic AI trên hạ tầng Cloud (AWS).
- Lan tỏa tư duy học tập suốt đời (Lifelong Learning): Đổi mới mental model, vượt qua nỗi sợ thất bại, chấp nhận thử nghiệm công nghệ mới để chuẩn bị cho tương lai tự động hóa.
- Chia sẻ kinh nghiệm thi đấu & thực thi dự án: Nhìn lại hành trình, kiến trúc kỹ thuật (Architecture), mô hình kinh doanh và bài học từ các đội đạt giải tại Agentic AI Build Week.

### Danh Sách Diễn Giả

- **Mr. Joseph Marazota** – Head of Technology in Asia, Amazon Web Services 
- **Mr. Nguyen Gia Hung** – Head of Solutions Architecture in Vietnam & Cambodia, Amazon Web Services 
- Đội OneTeam: Nguyễn Tấn Thông & đại diện team (Dự án KFC Agent / Conversational Ordering)
- Đội Signal Scout (Dream AI): Le Tan Luc, Do Hoang Hieu, Trieu Quoc Hao, Nguyen Van Duy Khiem, Nguyen Cong Minh, Nguyen Tran Minh Quan(Dự án Multi-Agent Corporate Strategy Analysis)
- Đội Plan V: Pham Tien Thuan Phat, Huynh Hoang Long, Le Minh Nghia, Tran Dai Vi, Nguyen An (Dự án SA Professional AI-Native Assistant)
- Đội 3K: Huỳnh An Khương, Nguyễn Quốc Huy, Ngô Quang Khôi, Hoàng Lê Thành Đức, Đặng Nguyễn Phước Lộc, Đặng Trường Hưng (Dự án SHEPHERD - Computer Vision & Crowd Tracking)
- Đội Six Pillar: Việt, Nguyễn Văn Linh, Nguyên, Minh Nhật, Huyền (Dự án Adaptive Workflow Engine for Anti-Money Laundering)

### Nội Dung Nổi Bật

#### Thông điệp truyền cảm hứng từ AWS Leadership (Mr. Joseph Marazota)
- Sự thay đổi Mental Model: 20 năm trước release phần mềm tính bằng quý/tuần, ngày nay với AI Agents, các bản phát hành tự động diễn ra theo từng phút.
- Thách thức rào cản cựu trào: Kỹ sư trẻ cần dám thử nghiệm, không bị trói buộc bởi tư duy cũ; AI và Robot (như 1 triệu robot tại kho Amazon) chỉ thực sự có giá trị khi con người định hướng và xây dựng dữ liệu/agent điều khiển.
- Học thông qua trải nghiệm (Hands-on): Khuyên sinh viên nên tham gia ít nhất 1 trận Hackathon mỗi năm để đưa lý thuyết vào thực hành.

#### Chuyển đổi sang kiến trúc ứng dụng mới - Microservice Architecture
**Đội OneTeam:**

- Bài toán: Đặt hàng qua App tạo ra nhiều ma sát (chuyển app, đăng ký tài khoản, chọn menu phức tạp). McDonald's từng thất bại với AI Drive-thru vì vấn đề Hallucination (người dùng nhận nhầm 100 gói gà).
- Giải pháp: Xây dựng Multi-channel Conversational Ordering Agent trực tiếp trên Zalo/WhatsApp, tương tác tự nhiên, xác nhận giỏ hàng chặt chẽ để tránh đặt nhầm.
- Kiến trúc & Chi phí: Sử dụng AWS Agent Core (lưu giữ Memory), Bedrock, WAF bảo vệ traffic, tích hợp TinyFish để cào dữ liệu menu KFC. Tối ưu chi phí hạ tầng cực rẻ (~0.006 USD/đơn hàng, ~$88 USD/tháng).

**Đội Signal Scout:**

- Bài toán: Doanh nghiệp thiếu công cụ gom nhặt và xâu chuỗi thông tin chiến lược/báo cáo rời rạc từ các đối thủ cạnh tranh.
- Giải pháp: Xây dựng hệ thống Multi-Agent cào dữ liệu (vượt qua Login Wall bằng TinyFish/Apify) và dùng Bedrock/Langfuse phân tích, dự báo chỉ số ROI khi chuyển đổi cấu trúc tổ chức.
- Tối ưu: Đề xuất chuyển đổi từ các công cụ 3rd party sang giải pháp AWS Native hoàn toàn để đảm bảo compliance và giảm chi phí từ $130/tháng xuống mức tối ưu hơn.

**Đội Plan:**

- Bài toán: Solutions Architect (SA) mất 2-3 ngày vẽ kiến trúc, tính chi phí và tạo file IaC (Terraform) khi khách hàng yêu cầu gấp trong đêm.
- Giải pháp: Xây dựng AI Assistant nhận input ngôn ngữ tự nhiên/Tài liệu policy -> Tự động vẽ Diagram trên Draw.io -> Tính bảng giá -> Render file Terraform tuân thủ các quy tắc nội bộ (Internal Rules/Security).

**Đội 3K:**

- Bài toán: Ùn tắc người tại sân bay, sự kiện, siêu thị.
- Giải pháp: Dùng Kinesis Video Stream kéo dữ liệu camera -> Sử dụng YOLOv8/v11 + BoT-SORT trên ECS Fargate để phát hiện/theo dõi người theo Zone -> Kết hợp Bedrock Agent phân tích, đưa ra cảnh báo điều phối nhân sự tự động.

**Đội Six Pillar:**

- Bài toán: 90-95% cảnh báo rửa tiền (AML) trong ngân hàng/Crypto là False Positive, tốn 20-25 USD cho mỗi lần kiểm tra thủ công.
- Giải pháp: Kiến trúc 3 Layer (Kinesis -> XGBoost/Bedrock Classifier -> Multi-subagent kiểm tra KYC/Money Flow/Sanction -> Double Check bằng LLM/Guardrails) giúp giảm thời gian điều tra từ 3 giờ xuống vài phút.

### Những Gì Học Được

#### Tư Duy Thiết Kế & Sản Phẩm

- **Business-First:** Công nghệ dù phức tạp đến đâu cũng không qua được rào cản về nghiệp vụ. Phải xác định đúng Pain Point của người dùng trước khi đâm đầu vào code.
- **Giới hạn phạm vi*: Trong 24h Hackathon, chỉ nên tập trung xây dựng MVP (Minimum Viable Product) giải quyết được luồng cốt lõi, tránh phình Scope dẫn đến không kịp demo.
- **Human-in-the-loop**: Với các lĩnh vực nhạy cảm (Tài chính, Ngân hàng, An ninh), AI chỉ đóng vai trò hỗ trợ/tối ưu năng suất (Co-pilot), quyền quyết định cuối cùng vẫn cần con người Review/Audit.

#### Kiến Trúc Kỹ Thuật

- **Tư duy Multi-Agent:** Phân tách nhiệm vụ cho từng Sub-agent chuyên biệt (Crawl agent, KYC agent, Analysis agent...) được điều phối bởi Orchestrator Agent mang lại hiệu quả cao hơn một Prompt đơn lẻ.
- **Quản lý Token & Chi phí:** Cần xử lý/lọc dữ liệu bằng code thuần (Data Sanitization) trước khi đưa vào LLM để cắt giảm chi phí Token và hạn chế Prompt Injection.
- **Xử lý Hallucination & Consistency:** Áp dụng các công cụ kiểm soát Guardrails, sử dụng cơ chế Double Check (2 LLM hoặc Rule-based) để đảm bảo tính nhất quán của dữ liệu đầu ra.

#### Kỹ Năng Mềm & Làm Việc Nhóm

- **Hạ cái "Tôi" xuống:** Mọi team đều trải qua xung đột (conflict), điều quan trọng là "chửi nhau xong rồi đi vào vấn đề", tập trung vào mục tiêu chung và không công kích cá nhân (Get Personal).
- **Phân công & Kỷ luật:** Cần làm rõ vai trò (AI Engineer, Data/SE, Business/Pitching) và quản lý thời gian nghiêm ngặt khi chạy Deadline xuyên đêm.

### Ứng Dụng Vào Công Việc

- **Áp dụng mô hình Multi-Agent vào các bài toán thực tế:** Phân tách công việc phức tạp thành các Sub-agent kết hợp với AWS Bedrock/Step Functions.
- **Tối ưu hóa quy trình R&D:** Sử dụng AI Agent để tự động hóa khâu đọc/tóm tắt tài liệu, cào dữ liệu cạnh tranh và khởi tạo code mẫu (Terraform/IaC).
- **Rèn luyện tinh thần "Show Up - Build - Pitch":** Sẵn sàng đăng ký các cuộc thi Hackathon/Workshop thực chiến để tích lũy kinh nghiệm, mở rộng Network và làm đẹp Portfolio/CV.

### Trải nghiệm trong event

Sự kiện phát trực tiếp **FCAJ x Agentic AI Build Week: Show Up. Build. Pitch. WIN!** đem lại không khí vô cùng bùng nổ, thực tế và giàu năng lượng trẻ:

#### Sự nâng đỡ từ Cộng đồng & Chuyên gia
- **Cảm nhận rõ tinh thần kết nối mạnh mẽ của cộng đồng AWS First Cloud AI Journey (FCAJ):** Các anh chị đi trước luôn sẵn sàng hỗ trợ kỹ thuật, góp ý kiến trúc và đồng hành cùng các đội thi.
- Những lời nhận xét, câu hỏi "xoáy" từ Ban giám khảo chuyên môn giúp các đội nhận ra lỗ hổng về mô hình kinh doanh cũng như cách tối ưu chi phí hạ tầng.

#### Không khí Hackathon chân thực
- Những khoảnh khắc "dở khóc dở cười": Thức xuyên đêm đến 3-4h sáng, nằm ngủ gục trên sàn nhà/balo, hít đất để chống buồn ngủ, uống hàng loạt lon RedBull, hay những pha Push lầm file .env chứa API Key lên GitHub.
- Sự nhiệt huyết, lăn xả của các bạn sinh viên/kỹ sư trẻ từ nhiều trường đại học (FPT, Swinburne,...) khi vừa hoàn thành bài thi vừa liên tục điều chỉnh Demo live trên sân khấu.


#### Một số hình ảnh khi tham gia sự kiện
<img src="/images/IMG_4205.png" alt="Sự kiện AWS Event 2" width="500px" />
<img src="/images/IMG_4206.png" alt="Sự kiện AWS Event 2" width="500px" />
<img src="/images/IMG_4209.png" alt="Sự kiện AWS Event 2" width="500px" />


> Buổi chia sẻ không chỉ là màn tôn vinh các đội chiến thắng mà còn là một "cuốn nhật ký thực chiến" truyền cảm hứng mạnh mẽ: Hãy cứ bước ra khỏi vùng an toàn, lập team, đăng ký Hackathon, trải nghiệm cảm giác thức đêm làm sản phẩm – vì những kỷ niệm và bài học đó chính là hành trang giá trị nhất trên con đường sự nghiệp.
