## Bước 1: Tạo ứng dụng expressjs bằng công cụ generator 
https://expressjs.com/en/starter/generator.html 
Bài viết này sẽ hướng dẫn bạn tạo thêm route trong API để thực hiện upload file

## Bước 2: Cài thư viện multer
https://www.npmjs.com/package/multer 
```
npm install --save multer
```
## Bước 3: Chỉnh sửa route api
Nếu bạn tạo project mới thì phải tạo file api.js mới, nếu dùng code mẫu ở trên thì bạn mở file api.js có sẵn trong thư mục routes để chỉnh sửa

- Bên dưới dòng nhúng thư viện bạn thêm lệnh nhúng thư viện Multer
```javascript
const multer  = require('multer')
var objUpload = multer( { dest: './tmp'}); // vào thư mục code tạo 1 thư mục tên là tmp
```

- Xuống cuối file trước dòng module.exports = router; bạn viết thêm lệnh tạo route
```javascript
router.post('/demo-upload',objUpload.single('hinh_anh'), demo_upload);
```
## Bước 4: Vào file controller để viết hàm xử lý upload

Trong code mẫu này tên file controller là xeMayController.js, bạn mở file này ra và thêm vào đầu file lệnh nhúng thư viên fs để gọi hàm làm việc với file
```javascript
var fs = require('fs'); // thư viện xử lý về file
```
Tiếp theo bạn viết thêm hàm xử lý việc upload file: 

- Trong thư mục public/ của code web, bạn tạo một thư mục con là uploads 

- Tiếp theo bạn viết code cho hàm upload như sau (Chú ý đọc kỹ lời giải thích trong hàm)

 
```javascript
exports.demo_upload = (req, res, next) => {
    try {
        if (fs.existsSync(req.file.path)) {
            // có tồn tại file
            // định nghĩa đường dẫn file mới để lưu trữ
            let file_path = './public/uploads/' + req.file.originalname;
            // có thể kiểm tra định dạng file ....
            console.log(req.file);
            // { kết quả log có các thuộc tính, bạn muốn kiểm tra gì thì req.file.xxxxx
            //     fieldname: 'anh',
            //     originalname: 'giao-dien-mua-hang.png',
            //     encoding: '7bit',
            //     mimetype: 'image/png',
            //     destination: './tmp',
            //     filename: '280990afd14931ba2d750ff76630544e',
            //     path: 'tmp/280990afd14931ba2d750ff76630544e',
            //     size: 676236
            //   }
            // ở đây tạm thời chỉ cho upload ảnh: 
            if (req.file.mimetype.indexOf('image') == -1) {
                // định dạng file chuẩn ảnh
                msg = 'Không đúng định dạng ảnh';
                return res.status(400).json({ error: 'Lỗi không đúng định dạng ảnh' });// thoát khỏi hàm
            }
            // đến đây là phù hợp điều kiện, chuyển file từ thư mục tmp và thư mục uploads để lưu trữ lâu dài
            fs.renameSync(req.file.path, file_path);
            // không có lỗi thì sẽ chạy xuống lệnh dưới đây, bạn ghép nối chuỗi thành link ảnh
            // chú ý: http://localhost:3000 là đoạn domain bạn cần thay đổi cho phù hợp với web của bạn
            let link_anh = 'http://localhost:3000/uploads/' + req.file.originalname;
            res.status(200).json({ error: null, link_anh: link_anh });
        } else {
            res.status(404).json({ error: 'Không có file upload' });
        }
    } catch (ex) {
        res.status(400).json({ error: ex.message });
    }
}
```
 Kết quả chạy trên postman để upload ảnh: Bạn thấy kết quả trả về có link ảnh vậy là thành công upload ảnh độc lập.
 Bạn có link ảnh rồi thì có thể ghép vào bài viết hoặc sản phẩm để sau hiển thị lên màn hình, như vậy bạn sẽ mang code upload này ghép vào code.
