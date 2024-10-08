# Phân tích tĩnh mã độc brbbot.exe

### `Họ và tên : Chử Đức Anh`
### `Mã sinh viên : B23DCAT004`

`Bài tập : Phân tích tĩnh mã độc brbbot.exe trong thư mục sample buoi 2.zip ( pass giải nén: infected). Xác định:`
1. Mã độc có bị virustotal gán nhãn độc hại hay không?
2. Mã độc có đạt kỹ thuật persistence?
3. Strings của file mã độc gợi ý điều gì?
4. Dấu hiệu file của mã độc xuất hiện trên máy nạn nhân?
5. Dấu hiệu về network của mã độc?
6. Mã độc sử dụng thuật toán gì để giải mã config?
7. Mã độc thuộc loại mã độc nào?

## Bài làm
1. Mã độc bị virustotal gán nhãn độc hại (63/73 security vendors flagged this file as malicious), nó có thể là 1 con `trojan hoặc ransomware`

2. Theo em tìm hiểu, kỹ thuật persistence là các kỹ thuật giúp mã độc tạo được sự tồn tại và duy trì hoạt động trong hệ thống, và mã độc này đã đạt được mục đích khi sử dụng kỹ thuật này ( đạt được persistence ), cụ thể theo em phân tích em nhận ra 2 ý nổi bật dưới đây:

    a. Mã độc đã thực hiện kỹ thuật persistence bằng cách chỉnh sửa registry, cụ thể là thêm mục "brbbot" vào vị trí HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run. Nhờ đó, mã độc sẽ tự động chạy mỗi khi hệ thống khởi động, giúp duy trì hoạt động mà không cần sự can thiệp từ người dùng.


    ![image](https://github.com/user-attachments/assets/dba89764-5892-4b64-8e17-9081cbb99300)

    ![image](https://github.com/user-attachments/assets/e78e879f-0f38-4dbe-8393-4e08bac0c457)

   ![image](https://github.com/user-attachments/assets/4ca7b3a7-fbda-4fe2-903f-5d0fd91b3f9b)


    b. Tuy nhiên, em cũng thấy mã độc sử dụng các hàm như GetStartupInfoW, WSAStartup, WSACleanup. Em nghĩ chúng có thể đóng vai trò quan trọng trong quá trình khởi động và duy trì hoạt động của mã độc. Đây có thể là cách mã độc ngụy trang dưới các ứng dụng phổ biến để tránh bị phát hiện. Ngoài ra em cũng để ý có 1 user-agent của Mozilla, em không biết liệu đây có phải kiểu ẩn mình dưới dạng tên của 1 trình duyệt chạy trong hệ thống không nên em chỉ để nó ở dạng tình nghi ạ.

    ![image](https://github.com/user-attachments/assets/6f0d7253-0d9f-45fb-a928-f0c8f668bab3)

    ![image](https://github.com/user-attachments/assets/273a8e8b-c35a-4a4a-9979-de2d400dd85a)



3. Trong strings của file mã độc, em tìm thấy những thông tin em nghĩ là có ích sau:

    a. Chuỗi `Agent Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; Trident/4.0)` cùng với phiên bản `HTTP/1.1` cho thấy mã độc có khả năng sử dụng user-agent giống trình duyệt để che giấu các kết nối mạng. Hoặc là em nghĩ nó có thể là user-agent của chính người tạo ra mã độc này ạ?

    b. Các chuỗi mã hóa: Các hàm `CryptEncrypt, CryptDestroyKey, và CryptCreateHash` là các API mã hóa của Windows. Em nghĩ nó có thể gợi ý mã độc đã sử dụng kỹ thuật mã hóa để bảo vệ dữ liệu của nó hoặc để thực hiện các cuộc tấn công ransomware tống tiền ạ hoặc có thể là nó lợi dụng API mã hoá của Win để giải mã hay mã hoá gì đó cho tránh bị phát hiện. `( Em có được tiếp xúc với cái này trong CTF attack defence, cụ thể là người tấn công sẽ mã hoá các flag dưới dạng base64 chẳng hạn thì người phòng thủ nếu chỉ filter để tìm chuỗi "flag" thì sẽ không thể filter được flag dạng base64 vừa bị tấn công vào và bỏ sót nó, vì vậy em cũng nghi ngờ nó là 1 cách làm tương tự )`
   
    c. Chuỗi liên quan đến thao tác file và Internet: Các hàm như `InternetReadFile, InternetConnectA, CreateFileA, DeleteFileA, CopyFileA, và WriteFile` cho thấy mã độc có khả năng tương tác với các tệp tin hệ thống và thực hiện các kết nối qua Internet, nó là dấu hiệu cho thấy mã độc có thể thực hiện các hành vi như tải xuống tệp, xóa dấu vết hoặc giao tiếp với máy chủ điều khiển từ xa.



5. Theo em trong cụ thể bài này - trường hợp này, dấu hiệu file mã độc này đã xuất hiện trên máy nạn nhân chính là sự có mặt của chương trình có tên Mozilla với Agent db 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; Trident/4.0)


6. Về network, theo em thấy chỉ có 1 vài điểm liên quan nằm ở trong hàm WinMain, mã độc có call `WinAPI CreateEventW` rồi sau đó call 1 loạt các hàm như sub_14001840 để đọc file copy file hay nhận chỉ thị từ Internet ạ em không chắc phần này lắm 


![image](https://github.com/user-attachments/assets/1e0992ba-6df1-41b0-9b5e-222e2bcc0263)


Sau khi đã nhận được chỉ thị, nó sẽ close kết nối Internet lại để tiếp tục ẩn mình


![image](https://github.com/user-attachments/assets/4393e141-5795-48ab-9b7a-7036efbfe7cc)


6. Em chưa hiểu câu hỏi lắm ạ, em cũng chưa tìm được chỗ nào trong pseudo-code của mã độc có chứa từ config ạ, em mới thấy mỗi trong strings có. Hay nó là 1 thuật ngữ gì đó liên quan đến sửa đổi ạ? 
Câu này em mong được anh giải đáp ạ!

7. Theo tất cả những gì mà em đã phân tích trên, em "mạnh dạn" đoán đây là 1 con `trojan botnet` ạ =)))
