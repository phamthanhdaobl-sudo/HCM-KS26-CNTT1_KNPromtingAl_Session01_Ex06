# HCM-KS26-CNTT1_KNPromtingAl_Session01_Ex06
BÀI 6: PHÂN TÍCH CƠ CHẾ "TRÀN CỐC NGỮ CẢNH" (TRUNCATION) TRONG LUỒNG TƯ VẤN CSKH
Phần 1. Giải thích hình tượng chiếc cốc nước
1.1. Context Window là gì?
Context Window (cửa sổ ngữ cảnh) là giới hạn lượng thông tin mà AI có thể tiếp nhận và sử dụng trong một lần xử lý hội thoại.

Có thể hình dung Context Window giống như một chiếc cốc nước có dung tích nhất định:
- Chiếc cốc: Đại diện cho Context Window của AI.
- Nước trong cốc: Đại diện cho dữ liệu hội thoại, bao gồm câu hỏi của khách hàng, câu trả lời của AI và các thông tin liên quan.
- Dung tích cốc: Đại diện cho giới hạn Token mà mô hình có thể xử lý.
- Nước mới đổ vào: Đại diện cho những tin nhắn mới trong cuộc trò chuyện.
AI không có khả năng ghi nhớ vô hạn tất cả nội dung đã trao đổi. Khi lượng dữ liệu vượt quá giới hạn của Context Window, hệ thống có thể phải loại bỏ hoặc tóm tắt một phần thông tin cũ để dành chỗ cho nội dung mới.








code2compass.substack.com


1.2. Hiện tượng Truncation (cắt gọt dữ liệu)
Truncation là hiện tượng một phần nội dung hội thoại bị cắt bỏ khi lượng Token vượt quá giới hạn ngữ cảnh mà AI có thể xử lý.

Trong tình huống của MartX:
1. Lượt 1: Khách hàng cung cấp tên Trần Văn B và mã đơn hàng MTX-9988. AI tiếp nhận và phản hồi chính xác.
2. Lượt 2–41: Nhân viên và khách hàng tiếp tục trao đổi về nhiều mặt hàng rau củ. Các câu trả lời dài của AI khiến lượng Token tăng nhanh.
3. Lượt 42: Khi Context Window gần hoặc vượt giới hạn, hệ thống loại bỏ một phần nội dung cũ, trong đó có thể bao gồm thông tin ở lượt 1.
4. Kết quả: AI không còn được cung cấp đầy đủ thông tin ban đầu nên hỏi lại mã đơn hàng. Nghiêm trọng hơn, AI còn gọi nhầm khách hàng là chị Mai thay vì anh Trần Văn B.
Tại sao AI lại quên thông tin?

Vì AI chỉ có thể dựa vào dữ liệu được cung cấp trong ngữ cảnh hiện tại để tạo câu trả lời. Khi thông tin ban đầu bị loại khỏi ngữ cảnh và không được lưu ở một hệ thống bộ nhớ khác, AI có thể không còn khả năng truy xuất chính xác thông tin đó.

Lưu ý: Truncation không nhất thiết luôn xóa toàn bộ tin nhắn cũ nhất. Cách cắt phụ thuộc vào cơ chế quản lý ngữ cảnh của hệ thống.
Phần 2. Phương án quản lý RAM bộ nhớ
Để tránh tình trạng tràn Context Window trong quá trình tư vấn khách hàng, MartX cần áp dụng hai phương án sau:
Phương án 1: Thao tác từ phía người dùng (User-side)
Mục tiêu: Giúp AI duy trì những thông tin quan trọng trong suốt phiên tư vấn.

Các thao tác đề xuất:
- Tóm tắt hội thoại định kỳ: Sau khoảng 10–15 lượt trao đổi, yêu cầu AI tổng hợp những thông tin quan trọng như tên khách hàng, mã đơn hàng, vấn đề đang gặp phải và hướng giải quyết.
- Sử dụng Prompt nhắc lại thông tin: Khi cuộc hội thoại kéo dài, nhân viên có thể gửi một câu lệnh yêu cầu AI tiếp tục dựa trên bản tóm tắt trước đó.
- Loại bỏ thông tin không cần thiết: Hạn chế những câu trả lời quá dài, nội dung lặp lại hoặc không liên quan đến vấn đề khách hàng.
- Xác nhận thông tin trước khi xử lý: Nếu AI không chắc chắn về tên hoặc mã đơn hàng, nhân viên cần kiểm tra lại thay vì để AI tự suy đoán.
Ví dụ Prompt sử dụng:
Hãy tổng hợp toàn bộ thông tin quan trọng trong cuộc hội thoại hiện tại thành một bản tóm tắt ngắn gọn để tiếp tục tư vấn.

Yêu cầu giữ lại:
1. Họ tên khách hàng.
2. Mã đơn hàng.
3. Địa điểm mua hàng.
4. Vấn đề khách hàng đang gặp phải.
5. Những thông tin đã xác nhận.
6. Trạng thái xử lý và bước tiếp theo.
Không tự suy đoán hoặc thay đổi thông tin khách hàng. Nếu thông tin nào chưa được xác nhận, hãy ghi rõ "Chưa xác nhận".




Phương án 2: Thiết kế hệ thống (System-side)
Mục tiêu: Xây dựng cơ chế lưu trữ thông tin độc lập với Context Window để AI không phụ thuộc hoàn toàn vào lịch sử hội thoại.

Các giải pháp đề xuất:

1. Sử dụng Database để lưu thông tin khách hàng

Thay vì chỉ lưu thông tin trong hội thoại, hệ thống MartX cần lưu dữ liệu quan trọng vào cơ sở dữ liệu riêng.

Ví dụ:
Trường dữ liệu	Giá trị
Họ tên	Trần Văn B
Mã đơn hàng	MTX-9988
Chi nhánh	MartX Q1
Vấn đề	Hỏi về chính sách hoàn tiền
Trạng thái	Đang tư vấn

Khi khách hàng tiếp tục đặt câu hỏi, hệ thống có thể truy xuất dữ liệu từ Database để cung cấp lại cho AI.

2. Áp dụng RAG (Retrieval-Augmented Generation)

RAG là phương pháp giúp AI tìm kiếm và truy xuất thông tin từ nguồn dữ liệu bên ngoài trước khi tạo câu trả lời.

Quy trình hoạt động:
Khách hàng đặt câu hỏi mới

Ví dụ: Đơn hàng của tôi có được hoàn tiền không?


Hệ thống truy xuất Database

Tìm mã đơn MTX-9988 và thông tin khách hàng


Đưa dữ liệu liên quan vào Context Window

Cung cấp thông tin đã xác thực cho AI


AI trả lời chính xác hơn

Tiếp tục tư vấn đúng khách hàng và đơn hàng


3. Giới hạn độ dài phản hồi của AI

Thiết lập hệ thống để AI trả lời ngắn gọn, tập trung vào câu hỏi của khách hàng, tránh tạo ra quá nhiều Token không cần thiết.

4. Phân loại dữ liệu theo mức độ quan trọng
- Dữ liệu quan trọng: Tên khách hàng, mã đơn hàng, số điện thoại đã xác minh.
- Dữ liệu nghiệp vụ: Sản phẩm, giá tiền, tình trạng giao hàng, chính sách hoàn tiền.
- Dữ liệu ít quan trọng: Những câu chào hỏi, nội dung trao đổi phụ và thông tin lặp lại.
Nhờ đó, hệ thống có thể ưu tiên giữ lại dữ liệu quan trọng và loại bỏ những thông tin ít cần thiết.
Phần 3. Kết luận và bài học rút ra
Qua tình huống tư vấn CSKH tại MartX, có thể thấy Context Window là bộ nhớ ngữ cảnh có giới hạn của AI. Khi cuộc hội thoại kéo dài và lượng Token vượt quá khả năng xử lý, hệ thống có thể cắt bỏ dữ liệu cũ, dẫn đến hiện tượng Truncation.

Để hạn chế vấn đề này, cần kết hợp hai giải pháp:
- User-side: Tóm tắt hội thoại định kỳ, sử dụng Prompt nhắc lại thông tin quan trọng và hạn chế nội dung dư thừa.
- System-side: Lưu thông tin khách hàng trong Database, sử dụng API hoặc cơ chế truy xuất dữ liệu phù hợp, đồng thời quản lý và tối ưu Context Window.
Trong đó, Database/API phù hợp để truy xuất chính xác mã đơn hàng và hồ sơ khách hàng; RAG có thể được sử dụng bổ sung để tìm kiếm chính sách hoàn tiền, đổi trả và tài liệu nghiệp vụ.

Bài học quan trọng: Không nên phụ thuộc hoàn toàn vào khả năng ghi nhớ của AI trong một phiên hội thoại dài. Những thông tin nghiệp vụ quan trọng phải được lưu trữ bên ngoài Context Window để có thể truy xuất khi cần thiết.
