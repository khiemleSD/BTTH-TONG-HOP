# Bài tổng hợp PowerShell: dựng project quan-ly-sinh-vien

Lớp 2E35, môn IT101

## Phần 1. CLI vs GUI và cấu trúc câu lệnh

### Câu 1

Câu lệnh `ls -Force C:\\\\\\\\Windows\\\\\\\\Logs` gồm ba thành phần:

|Thành phần|Trong câu lệnh|Ý nghĩa|
|-|-|-|
|Command|`ls`|Lệnh liệt kê nội dung thư mục (alias của `Get-ChildItem`)|
|Option/Flag|`-Force`|Hiển thị thêm cả file ẩn và file hệ thống|
|Argument|`C:\\\\\\\\Windows\\\\\\\\Logs`|Thư mục mà lệnh tác động lên|

### Câu 2

Máy chủ Cloud thường không có giao diện đồ họa (còn gọi là headless). Chạy desktop trên server tốn RAM và CPU mà người thuê trả tiền theo cấu hình, nên nhà cung cấp chỉ để lại dòng lệnh. Nhóm kết nối từ xa qua SSH, và kênh này chỉ truyền chữ, nên không có cửa sổ hay chuột để dùng. Muốn tạo thư mục, chép project hay chạy chương trình trên server thì chỉ còn cách gõ lệnh.

## Phần 2. Điều hướng hệ thống file

### Câu 1

```powershell
cd .\\\\\\\\quan-ly-sinh-vien
```

Dấu `.` là thư mục hiện tại (Desktop), nên `.\\\\\\\\quan-ly-sinh-vien` là đường dẫn tính từ vị trí đang đứng. Bỏ `.\\\\\\\\` đi vẫn chạy được.

### Câu 2

```
C:\\\\\\\\Users\\\\\\\\student\\\\\\\\Desktop\\\\\\\\quan-ly-sinh-vien\\\\\\\\src\\\\\\\\main.py
```

### Câu 3

```powershell
ls -Force ..\\\\\\\\data
```

Dấu `..` lùi lên thư mục cha (`quan-ly-sinh-vien`), rồi `\\\\\\\\data` đi xuống thư mục cùng cấp với `src`. Cờ `-Force` hiện cả file ẩn.

## Phần 3. Thao tác file và thư mục

### Câu 1

```powershell
mkdir quan-ly-sinh-vien\\\\\\\\src, quan-ly-sinh-vien\\\\\\\\data
```

PowerShell tự tạo `quan-ly-sinh-vien` vì nó nằm trong đường dẫn của cả hai thư mục con. Dấu phẩy ngăn cách hai đường dẫn, nên một lệnh tạo được cả hai.

### Câu 2

PowerShell đang đứng trong `quan-ly-sinh-vien`:

```powershell
New-Item src\\\\\\\\main.py, src\\\\\\\\utils.py, README.md -ItemType File
```

Hai file Python nằm trong `src`, còn `README.md` nằm ở thư mục gốc đúng như cấu trúc ở mục 3a.

### Câu 3

Từ Desktop, đổi tên thư mục:

```powershell
mv quan-ly-sv quan-ly-sinh-vien
```

Cmdlet đầy đủ cho lệnh này là `Rename-Item quan-ly-sv quan-ly-sinh-vien`.

### Câu 4

```powershell
cp quan-ly-sinh-vien backup-quan-ly-sinh-vien -Recurse
```

Cờ `-Recurse` bắt lệnh chép cả nội dung bên trong. Thiếu cờ này, PowerShell chỉ tạo thư mục backup rỗng.

## Phần 4. Xử lý lỗi Terminal

### Câu 1. Log lỗi số 2

Đây là lỗi gõ sai tên. Lệnh `cd scr` thiếu chữ `r` đúng vị trí, vì thư mục thật tên là `src`, nên PowerShell báo không tìm thấy đường dẫn.

Dùng Tab để kiểm tra: gõ `cd s` rồi nhấn Tab. PowerShell tự điền tên thư mục bắt đầu bằng chữ `s` đang có trong vị trí hiện tại. Nếu nó điền ra `src` thì thư mục tồn tại và lỗi do gõ sai. Nếu Tab không điền gì, thư mục không có ở đây, lúc đó gõ `ls` để xem danh sách thật và kiểm tra lại vị trí đang đứng.

### Câu 2. Log lỗi số 3

`Access is denied` là lỗi thiếu quyền, và trên Windows nguyên nhân thường gặp là PowerShell chưa chạy với quyền Administrator. Tài khoản thường không đủ quyền để thực hiện các thao tác mà script cài đặt yêu cầu.

Cách khắc phục: đóng cửa sổ hiện tại, gõ PowerShell vào ô tìm kiếm của Windows, nhấn Ctrl+Shift+Enter để mở bằng quyền Administrator. Sau đó `cd` lại về thư mục chứa script và chạy lại:

```powershell
.\\\\\\\\setup.ps1
```

### Câu 3. Log lỗi số 1

PowerShell cảnh báo vì `old-version` có file bên trong, mà lệnh `rm` không kèm `-Recurse` thì không rõ người dùng có muốn xóa luôn phần nội dung đó hay không. Nó hỏi lại để tránh xóa nhầm, vì file xóa bằng lệnh này không đi qua Thùng rác nên không lấy lại được.

Lệnh để xóa luôn không bị hỏi:

```powershell
rm old-version -Recurse -Force
```

`-Recurse` xóa cả nội dung bên trong và bỏ câu hỏi xác nhận. `-Force` xử lý thêm các file ẩn hoặc chỉ-đọc. Trước khi chạy, gõ `ls old-version` để chắc chắn đúng thư mục cần xóa.

### Câu 4. Log lỗi số 4

|Bước|Câu trả lời|
|-|-|
|(a) Dòng chứa thông điệp lỗi trực tiếp nhất|Dòng cuối cùng: `FileNotFoundError: \\\\\\\[Errno 2] No such file or directory: 'data/input.csv'`|
|(b) Từ khóa lỗi|`FileNotFoundError` và `No such file or directory`|
|(c) Điều cần kiểm tra tiếp|Vị trí đang đứng là `...\\\\\\\\quan-ly-sinh-vien\\\\\\\\src`, mà code mở `data/input.csv` bằng Relative Path, và Relative Path được tính từ thư mục chạy lệnh. Python tìm `src\\\\\\\\data\\\\\\\\input.csv` nên không thấy, trong khi file thật nằm ở `quan-ly-sinh-vien\\\\\\\\data\\\\\\\\input.csv`.|

Có hai cách sửa. Cách thứ nhất là lùi ra thư mục gốc rồi chạy:

```powershell
cd ..
python src\\\\\\\\main.py
```

Cách thứ hai là sửa đường dẫn trong code thành `../data/input.csv` để chạy được từ trong `src`.



