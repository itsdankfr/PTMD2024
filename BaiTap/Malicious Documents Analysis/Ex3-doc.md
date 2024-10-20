# Malicious Documents Analysis

Mở file lên, enable editing và macro sau đó dùng tổ hợp `ALT F11` để view phần doc3's VBA, ta có được cái nhìn tổng quan:

![image](https://github.com/user-attachments/assets/31d4ce8d-2675-462c-b58a-453ced1fd18d)

Bắt đầu debug chương trình, ngay từ phần nội dung đầu tiên đã thấy rất nhiều lần lặp lại tại 1 biến, vì nghi ngờ khả năng biến này lặp lại để nối các kí tự tạo thành string có nghĩa nên em đã đặt 1 lệnh in `Debug.Print` và `add Watch` ở dưới để theo dõi value của biến này sau mỗi lần lặp, và sau khi nó chạy xong ta được 1 string có nghĩa dưới đây:

![image](https://github.com/user-attachments/assets/9d5d6b46-35e0-4b18-84bf-cc334d19e146)

Tiếp tục với lấy value 1 loạt các biến được lặp lại với logic tương tự như trên, em được 1 loạt các strings là Path hoặc Windows API có nghĩa, sau khi ghép chúng lại em được:
```
OneDrive.exe
tmp.b64
Scripting.FileSystemObject
WScript.Shell
cmd /C schtasks.exe /create /sc minute /mo 1 /tn OneDrive /tr winmgmts:\\.\root\cimv2
Win32_ProcessStartup
winmgmts:\\.\root\cimv2:Win32_Process
```
Ngoài ra, khi trỏ vào các biến để check value, em có thể thấy được Path của 2 file Onedrive.exe và tmp.b64, cụ thể: `C:\Users\Chu\AppData\Local\Temp\Onedrive.exe` và `C:\Users\Chu\AppData\Local\Temp\tmp.b64`.

Đó là endpoint của doc3's VBA. Sau khi mò mẫm 1 hồi, em có thể hiểu được đoạn code VBA này đang call cmd để chạy command lên lịch cho việc thực thi tác vụ có tên Onedrive.exe (malicious) với path trên mỗi phút. Hơn nữa, khi thử decode file tmp.b64 ta được chính file với nội dung hoàn toàn khớp với file Onedrive.exe? Có lẽ giờ sẽ cần phải đến phần phân tích các hành vi của Onedrive.exe do đó là manh mối duy nhất ta có sau khi kết thúc phân tích đoạn code Visual Basic trên.

Đầu tiên, tương tự tất cả các bài liên quan đến `.exe` khác em ném nó vào `Detect it Easy` và nhận được kết quả trả về là PE64 được biên dịch bằng `C# .Net`

![image](https://github.com/user-attachments/assets/d61aee6b-6535-4631-b0f4-831b2d8d3fd9)

Vì là .Net nên ta sẽ sử dụng `DnSpy` để Reverse chương trình này, ném vào DnSpy rồi `Go to entry point`, ta có góc nhìn tổng quát:

![image](https://github.com/user-attachments/assets/38bae944-867d-4125-a918-882792b25c9b)


Trong hàm Agent, em có để ý nó lấy các giá trị như Hostname, OSversion? Em chưa rõ mục đích nhưng em đang hướng đến việc nếu đây là một con Botnet, việc lưu lại các data về Hostname hay OSversion có thể để phân loại trong 1 mạng lưới các Zombie đã bị nhiễm độc? Một phần lí do em hướng đến việc đây là 1 con Botnet là bởi em có thể tìm thấy các hàm Decrypt, Encrypt các thông điệp được truyền đi và gửi lại từ C&C của mã độc cùng với đó 1 vài hàm có chức năng Send Resposnes, Get Requests với method HTTP ở trong:

![image](https://github.com/user-attachments/assets/1354a3b2-7a91-44da-9ae8-c805826ed0c0)


Tại endpoint, em thấy nó trỏ đến 1 Website và theo em tìm hiểu Botnet thường sử dụng các máy chủ web hoặc dịch vụ trực tuyến làm trung gian để trao đổi dữ liệu giữa C&C và các Zombie. Vì vậy, khả năng cao là đó là điểm liên lạc của C&C để điều khiển mạng botnet ạ?

![image](https://github.com/user-attachments/assets/8477f5ef-28b2-4528-a06f-f79a68e0d3e0)

Tóm lại sau khi phân tích, em xin đưa ra kết luận đây là một con Botnet ạ:>!
