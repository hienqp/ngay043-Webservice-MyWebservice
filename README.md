ANDROID WEBSERVICE
___
- sử dụng Online Sever - MySQL - PHP - JSON để tương tác giữa thiết bị Android và Online Database.
- thiết bị Android sẽ lấy dữ liệu từ Server qua trung gian định dạng JSON.
- thiết bị Android có thể upload trực tiếp dữ liệu lên Server.
- Database Online sẽ được quản lý bởi PHP và MySQL.
___

MÔI TRƯỜNG GIẢ LẬP SERVER

- XAMPP
- sau khi cài đặt XAMPP tiến hành start Apache và MySQL
    - Apache dùng để giả lập Server (port mặc định là 80,443)
    - MySQL dùng để quản lý Database

- lưu ý Apache hay gặp lỗi port 80,443
___

CHƯƠNG TRÌNH VIẾT CODE PHP

- cài đặt SublimeText
___

TEST SERVER GIẢ LẬP

- sau khi start Apache, sử dụng trình duyệt bất kỳ, nhập địa chỉ ``localhost:80``
- nếu Apache được start thành công, thì trình duyệt sẽ hiển thị trang chủ mặc định của XAMPP.
- trong thư mục cài mặc định của XAMPP (thường là ``C:\xampp``)
    - tìm thư mục ``htdocs``
    - tạo 1 thư mục mới bất kỳ trong ``htdocs`` (ví dụ: ``androidwebservice``)
    - trong thư mục ``androidwebservice`` tạo 1 file ``kiemtra.txt``
    - nhập nội dung bất kỳ vào file ``kiemtra.txt`` (ví dụ ``học android với webservice``)
    - từ trình duyệt nhập địa chỉ ``localhost:80/androidwebservice/kiemtra.txt``
    - nếu thành công trình duyệt sẽ hiển thị ``học android với webservice``
___

TEST VIẾT FILE PHP TRẢ VỀ DỮ LIỆU DẠNG JSON

- trong thư mục ``androidwebservice`` tạo 1 file ``demo.php``
- trong file ``demo.php`` code nội dung theo cú pháp của PHP như sau:

```php
<?php
	echo "Android Webservice";
?>
```

- từ trình duyệt nhập địa chỉ ``localhost:80/androidwebservice/demo.php``, nếu trình duyệt hiển thị ``Android Webservice`` là thành công.

___

LÀM QUEN PHP

- thẻ mở ``<?php``
- thẻ đóng ``?>``
- nội dung của file PHP sẽ nằm giữa thẻ mở và thẻ đóng
- khai báo biến trong PHP, dấu ``$`` nằm ngay trước tên biến sau đó là dấu ``=`` gán giá trị, kết thúc bằng dấu ``;``
  - ``$a = 5;``
  - ``$b = "12ABC";``
- quy tắc cộng giá trị với dấu ``+``
  - dấu ``+`` sẽ cộng giá trị số học của các con số hoặc của các con số trong chuỗi với điều kiện là các con số đầu tiên trong chuỗi
  - ``1 + "12abc" = 13``
  - ``"12ccc + 12ccc = 24``
  - ``1 + 2 = 3``
- quy tắc nối chuỗi với dấu ``.``, dấu ``.`` được hiểu là việc nối chuỗi, dù cho đó là bất cứ giá trị nào
  - ``1 . "12ccc" = 112ccc``
  - ``9 . 9 = 99``
  - ``"12mmm . 12mm = 12mmm12mm``

___

VÍ DỤ VỀ FILE PHP TRẢ DỮ LIỆU VỀ DẠNG JSON

- file ``demo.php``

```php
<?php
// 1. Tạo class SinhVien
class SinhVien{
	public $HoTen;
	public $NamSinh;
	public $DiaChi;

	public function __construct($hoten, $namsinh, $diachi){
		$this->HoTen = $hoten;
		$this->NamSinh = $namsinh;
		$this->DiaChi = $diachi;
	}
}
// 2. Tạo mảng
$mangSV = array();

// 3. Thêm phần tử vào mảng
array_push($mangSV, new SinhVien("Nguyen Van A", 1999, "Ha Noi"));
array_push($mangSV, new SinhVien("Nguyen Van B", 1995, "Ho Chi Minh"));

// 4. Chuyển định dạng của mảng -> JSON
echo json_encode($mangSV);
?>
```

- từ trình duyệt nhập địa chỉ ``localhost:80/androidwebservice/demo.php``, nếu trình duyệt hiển thị ``[{"HoTen":"Nguyen Van A","NamSinh":1999,"DiaChi":"Ha Noi"},{"HoTen":"Nguyen Van B","NamSinh":1995,"DiaChi":"Ho Chi Minh"}]`` là thành công.

- sau khi kiểm tra truy cập file ``demo.php`` thành công, tiến hành tạo 1 project để đọc database từ app.
- tạo project ``MyWebservice``
- sử dụng API Volley để đọc database từ Web, search Volley từ developer.android.com

```
    dependencies {
        ...
        implementation 'com.android.volley:volley:1.1.1'
    }
```

- copy dòng ``implementation 'com.android.volley:volley:1.1.1'`` vào block ``dependencies`` trong ``build.gradle`` cấp module.
- sau khi khai báo sử dụng API Volley tiến hành Sync Now

- khai báo ``<uses-permission android:name="android.permission.INTERNET"/>`` trong ``AndroidManifest.xml``

- trong class MainActivity khai báo 1 method đọc JSON từ String url của localhost

```java
package com.hienqp.mywebservice;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.widget.Toast;

import com.android.volley.Request;
import com.android.volley.RequestQueue;
import com.android.volley.Response;
import com.android.volley.VolleyError;
import com.android.volley.toolbox.JsonArrayRequest;
import com.android.volley.toolbox.Volley;

import org.json.JSONArray;

import java.lang.reflect.Method;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    private void ReadJSON(String url) {
        RequestQueue requestQueue = Volley.newRequestQueue(this);
        JsonArrayRequest jsonArrayRequest = new JsonArrayRequest(Request.Method.GET, url, null,
                new Response.Listener<JSONArray>() {
                    @Override
                    public void onResponse(JSONArray response) {
                        Toast.makeText(MainActivity.this, response.toString(), Toast.LENGTH_SHORT).show();
                    }
                },
                new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        Toast.makeText(MainActivity.this, error.toString(), Toast.LENGTH_SHORT).show();
                    }
                });
        requestQueue.add(jsonArrayRequest);
    }
}
```

- trong method ``onCreate`` tiến hành gọi method ``ReadJSON`` và truyền vào String url ``"http://localhost/androidwebservice/demo.php"`` của file ``demo.php`` từ localhost XAMPP.

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ReadJSON("http://localhost/androidwebservice/demo.php");
    }
```

- tiến hành chạy thử kiểm tra app Toast error ``NoConnectionError`` app không connect được tới String url, bởi vì Android không hiểu localhost là cái gì, để giải quyết, trong String url thay thế ``localhost`` bằng địa chỉ IPV4 của Internet đang sử dụng, có thể nhập thêm số port nếu cần.
- sau khi chỉnh sửa.

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ReadJSON("http://192.168.1.8:80//androidwebservice/demo.php");
    }
```

- ngoài ra, từ API 28 trở lên, trong ``AndroidManifest.xml`` ta cần thêm ``android:usesCleartextTraffic="true"`` vào thẻ ``application``.
- sau khi hoàn tất chỉnh sửa tiến hành test lại app.

___

TẠO DATABASE MySQL

- để mở được Database MySQL trong localhost ta có 2 cách:
  - từ trình duyệt truy cập vào localhost, click chọn thẻ phpMyAdmin
  - từ giao diện XAMPP, trên dòng MySQL click button Admin
- sau khi truy cập được vào trình quản lý phpMyAdmin (là nơi quản lý Database) ta tiến hành tạo mới 1 database
  - click chọn ``new`` ở cột bên trái, nơi quản lý danh sách các database
  - tiến hành thiết lập các thông tin cho database mới
    - Database name: ``sinhvien``
    - Collation: ``utf8_general_ci`` (để hiển thị cả tiếng việt có dấu)
    - click Create để khởi tạo database
  - sau khi tạo thành công database, tiếp tục tạo table trong database
    - nhập tên của table: ``student``
    - chọn số columns cho table: ``4`` (id, ten, namsinh, diachi)
    - click Create để tạo table
  - sau khi tạo table thành công, giao diện structure thiết lập thông số cho table sẽ hiện ra, nếu muốn thêm column vào table ta có thể nhập số column và click Go
    - ``id``: Name - id, Type - INT, click chọn checkbox A..I (Auto Increment - tự động tăng dần đối với id), Index - PRIMARY (Chọn id làm khóa chính)
    - ``hoten``: Name - hoten, Type - VARCHAR, Lenght - 150
    - ``namsinh``: Name - namsinh, Type - INT
    - ``diachi``: Name - diachi, Type - VARCHAR, Lenght - 255
    - click ``Save`` để lưu lại thông số của table
  - sau khi Save table vừa tạo, giao diện quản lý cấu trúc (Structure) của bảng sẽ hiển thị, ở giao diện này ta có thể thêm, xóa, sửa bất kỳ column nào nếu muốn (nên chỉnh sửa trước khi thêm data vào table, nếu chỉnh sửa table đã có data rất dễ xảy ra lỗi)
  - để thêm data thủ công vào table, ta chọn thẻ Insert, giao diện nhập liệu sẽ hiển thị, ta có thể thêm đơn lẻ hoặc 1 lần 2 dòng data vào table, nếu thêm 2 dòng data cùng lúc thì lúc hoàn thành Insert click button Go phía dưới cùng
  - thẻ Browse dùng để duyệt table, tại thẻ này ta dùng để kiểm tra data trong table, thêm xóa sửa data trong table

  ___

  
