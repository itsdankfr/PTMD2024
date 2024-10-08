# Phân tích tĩnh mã độc brbbot.exe

Họ và tên : Chử Đức Anh
Mã sinh viên : B23DCAT004

Bài tập : Phân tích tĩnh mã độc brbbot.exe trong thư mục sample buoi 2.zip ( pass giải nén: infected). Xác định:
1. Mã độc có bị virustotal gán nhãn độc hại hay không?
2. Mã độc có đạt kỹ thuật persistence?
3. Strings của file mã độc gợi ý điều gì?
4. Dấu hiệu file của mã độc xuất hiện trên máy nạn nhân?
5. Dấu hiệu về network của mã độc?
6. Mã độc sử dụng thuật toán gì để giải mã config?
7. Mã độc thuộc loại mã độc nào?

Bài làm
Mã độc bị virustotal gán nhãn độc hại (63/73 security vendors flagged this file as malicious), cụ thể nó có thể là 1 con trojan hoặc ransomware


Theo em tìm hiểu, kỹ thuật persistence là các kỹ thuật giúp mã độc tạo được sự tồn tại và duy trì hoạt động trong hệ thống, và mã độc này đã đạt được mục đích khi sử dụng kỹ thuật này ( cụ thể theo em phân tích nó thực hiện kỹ thuật persistence bằng cách run ngay từ khi bật máy qua GetStartupInfoW và sau đó là WSAStartup, WSACleanup rồi ẩn mình dưới tên trình duyệt Firefox Mozilla )


Trong strings của file mã độc, em tìm thấy những thông tin em nghĩ là có ích sau:
a. Agent db 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; Trident/4.0)' và HTTP szVersion db 'HTTP/1.1',0. Theo những suy luận của em, file mã độc này sẽ ẩn mình dưới tên của 1 trình duyệt nổi tiếng cụ thể ở đây là Firefox Mozilla mục đích để nếu trong máy của đối tượng bị tấn công có trình duyệt này. Họ sẽ rất khó để nhận diện được file mã độc.

b. Các strings liên quan đến mã hoá dữ liệu như CryptEncrypt, CryptDestroyKey, CryptCreateHash đây là dấu hiệu cho mã độc này có thể là 1 con ransomware để tống tiền nạn nhân ( vì encrypt bằng hash - hàm băm nên không thể giải mã nếu không có key, mà hash thì là loại mã hoá bất đối xứng )

c. Những thao tác với tệp hay với Internet như InternetReadFile, InternetConnectA, CreateFileA, DeleteFileA, CopyFileA, và WriteFile cũng có thể là 1 dấu hiệu của việc mã độc này sẽ 


Theo em trong cụ thể bài này - trường hợp này, dấu hiệu file mã độc này đã xuất hiện trên máy nạn nhân chính là sự có mặt của chương trình có tên Mozilla với Agent db 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; Trident/4.0)


Về network, 
