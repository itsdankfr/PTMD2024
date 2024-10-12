# Bài tập phân tích động mã độc
## `Chử Đức Anh - B23DCAT004`
# Vì cài đặt môi trường ảo gặp nhiều bugs nên em hơi mất thời gian ngày hôm nay ạ, em sẽ cố hoàn thành nhanh hiện em vẫn đang làm tiếp ạ em chi sub bài trước

### Instructions
Phân tích động mẫu Lab3-2.dll trong 'sample buoi 3.zip' pass infected. Bạn hãy trả lời các câu hỏi sau:
1. Cách cài đặt mã độc?
2. Mã độc đạt được persistence như thế nào?
3. Cách chạy mã độc sau khi cài đặt?
4. Xác định PID của tiến trình đang chạy mã độc.
5. Dấu hiệu host-based(file, process, registry, service) trên máy bị nhiễm mã độc?
6. Dấu hiệu về network trên máy bị nhiễm mã độc?


### 1 : Cách cài dặt mã độc
Tải file từ Teams, em nhận được 1 file .dll ( dynamic link library ). Vì trước đó đã làm 1 bài trong PTITCTF phải debug loại file này nên em biết phải dùng command `rundll32.exe` với đúng tham số truyền vào để cài đặt được 1 file dll


![image](https://github.com/user-attachments/assets/a6a9fe79-6abf-48e7-90d0-5bc280f37e2e)


### 2 :


### 3 :
Cách thực thi như em đã đề cập ở trên, em có 2 cách nhưng về bản chất đều là 1
1 : sử dụng cmd => rundll32.exe filename, [entry point] [args] => rundll32.exe Lab3-2.dll, Install
2 : 


![Screenshot 2024-10-12 231844](https://github.com/user-attachments/assets/c6db3f22-f4fd-4a10-8938-6f22cf5c2279)
