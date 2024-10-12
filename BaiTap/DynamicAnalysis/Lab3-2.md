# Bài tập phân tích động mã độc
## `Chử Đức Anh - B23DCAT004`

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


### 2 : Mã độc đạt được persistence như thế nào?
Ở bài này em chưa phân tích được cụ thể cách mã độc đạt được persistence bằng phân tích động ạ. 
Em có thử phân tích tĩnh, cụ thể là đọc source thì em thấy ở đoạn đầu này ( dòng 40 đến 50 hàm Install ) có việc xử lí Registry Key có vẻ khả nghi do: 

![image](https://github.com/user-attachments/assets/615cc67a-02de-4305-a8ed-24078df5a1bf)

lần 1 gọi hàm check thì truyền vào SubKey 1 giá trị
![image](https://github.com/user-attachments/assets/b1245bc7-3bab-4d17-be83-c7f522f202ac)

lần 2 gán thì lại dùng ValueName với 1 database khác?
![image](https://github.com/user-attachments/assets/2ae88bd9-230f-44b6-bb39-e8712580d6b5)



### 3 :
Cách thực thi như em đã đề cập ở trên, em có 2 cách nhưng về bản chất đều là 1
Cách đầu tiên là sử dụng cmd => rundll32.exe filename, [entry point] [args] => rundll32.exe Lab3-2.dll, Install ( Hình ảnh mã độc được ghi lại trong `processhacker` sau khi thực thi )


![Screenshot 2024-10-12 231844](https://github.com/user-attachments/assets/c6db3f22-f4fd-4a10-8938-6f22cf5c2279)


Cách thứ 2 : chọn local Windows debugger trong IDA và truyền các tham số này vào process options, debug nhưng mình chưa đặt breakpoint nào thì nó vẫn run hết cả chương trình như bình thường


![image](https://github.com/user-attachments/assets/e4209bad-fde9-4f08-afb5-b0299ea57242)


### 4. Xác định PID của tiến trình đang chạy mã độc.
Khi run file mã độc, có 1 tệp khả nghi tên WerFault.exe xuất hiện đi kèm với file rundll32.exe, vì vậy em nghi ngờ nó là mã độc và PID của nó em tìm được trong `process hacker` là 7696, 

![image](https://github.com/user-attachments/assets/aadacea5-f096-4afa-aca3-052e3ee19ebd)


dưới đây là ảnh khi em filter trong `process monitor`


![image](https://github.com/user-attachments/assets/c95df3dd-b271-4032-96e8-6fe5ab0aa732)


### 5. Dấu hiệu host-based(file, process, registry, service) trên máy bị nhiễm mã độc?
Sau khi filter theo PID 7696 để tìm các tiến trình liên quan đến mã độc, em tìm được các dấu hiệu cho việc thay đổi registry ( Open, Set Info Key, Close Key rất nhiều )


![image](https://github.com/user-attachments/assets/285be049-065b-44f1-b91a-7f8ab1a11a68)


và cũng có các process liên quan đến file cụ thể như Write, Create, Open, ...


![image](https://github.com/user-attachments/assets/f9265bea-7fb2-4e52-8971-0db1df5e1286)


Dù tìm được nhưng do khả năng phân tích động của em còn chưa tốt, em mới chỉ dừng lại ở mức tìm được chứ chưa rõ làm sao để có thể phân tích nó đã làm cụ thể gì trong từng tiến trình ấy ạ.
Ngoài ra em có tìm được thông tin giống bài buổi trước, cụ thể mã độc này chỉ hoạt động được ở trên `Windows XP 6.11`, em tìm thấy khi strings trong IDA

![image](https://github.com/user-attachments/assets/dfb6751b-85eb-47b3-b0d4-a00fc9b12318)

### 6 : Dấu hiệu về network trên máy bị nhiễm mã độc?
Ở cả 2 phần quan trọng của mã độc này ( Install và Uninstall ) em đều thấy có chứa comment bên phần ASM `IPRIP`. Sau một hồi tìm kiếm thông tin thì em được biết IPRIP là 1 phương thức, giao thức mạng cũng giống như là HTTP nên đây là 1 dấu hiệu sẽ liên quan đến network. Em chưa sử dụng mấy nhưng em nghĩ có thể User bị nhiễm mã độc sử dụng wireshark để check xem có phương thức bất thường nào là IPRIP vừa thực hiện vài tiến trình thì đó có thể là dấu hiệu. Ngoài ra em cũng chưa tìm được dấu hiệu nào khác bởi sau khi Install mã độc tự kill chính mình bằng hàm Uninstall để xoá dấu vết.
