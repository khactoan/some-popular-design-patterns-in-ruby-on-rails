# Một vài design patterns phổ biến thường được sử dụng trong Ruby on Rails

Chắc hẳn hầu hết các developers đều đã từng làm việc hoặc biết đến mô hình [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) trong phát phần mềm. Và dĩ nhiên Rails framework cũng được phát triển dựa trên mô hình đó.
Với MVC, models chính là thành phần trung tâm nơi mà chúng ta thường viết các business logic, các hàm xử lý phức tạp. 

![enter image description here](https://miro.medium.com/max/1256/1*8hkrKsTgJa6fIliAVWledQ.jpeg)

Khi vừa mới làm việc với các dự án dựa trên mô hình MVC nói chung hay 
Rails famework nói riêng, các developers thường sẽ có tư tưởng để hết logic vào models với quan niệm "**Skinny Controllers, Fat Models**". 

![enter image description here](https://miro.medium.com/max/1400/1*qFKKaOlJD47EC-8lNzJdBg.jpeg)

Với cách thiết kế như này thì models của chúng ta sẽ càng ngày càng phình to, khó maintain và dĩ nhiên việc duplicate code cũng không thể tránh khỏi. 

Hiểu rõ hơn về trách nhiệm của models trong Ruby on Rails đó là phụ trách **Active Record**, thao tác dữ liệu với các relational database tương ứng. Mỗi class hay mỗi thành phần nên có một trách nhiệm duy nhất các bạn có thể tham khảo thêm ở khái niệm [SOLID](https://en.wikipedia.org/wiki/SOLID) trong lập trình hướng đối tượng.

> Say No to ‘Skinny Controller, Fat Model’ and Yes to ‘Skinny Controller, Smart Model with Single Responsibility’

Vậy nên viết như thế nào để những dòng code của chúng ta được rõ ràng hơn, tối ưu hơn và quan trọng là dễ dàng scale up với các tính năng, requirement được bổ sung thêm về sau. Bài viết dưới đây mình xin phép được chia sẻ một số design patterns thường được áp dụng trong các dự án Ruby on Rails.
 1. Service Objects
 2. View Objects
 3. Query Objects
 4. Decorators
 5. Form Objects
 6. Value Objects
 7. Policy Objects

## 1. Service Objects
Service Object là một đối tượng Ruby (PORO - Plain Old Ruby Object) được dùng để đóng gói các business logic hay các tính toán phức tạp vào các lớp và phương thức để quản lí.

Những trường hợp nên dùng Service Objects: 
 - Khi cần thực thi các tính toán phức tạp hay các business logic, ví dụ như cần tính lương của nhân viên dựa trên ngày chấm công.
 - Khi cần sử dụng đến các API như khi thanh toán trực tuyến qua [Stripe](https://stripe.com/)
 - Khi import CSV với một lượng data lớn và các validation
 - Khi xóa, dọn dẹp các dữ liệu dư thừa trong database một cách hiệu quả mà không ảnh hưởng đến dữ liệu hiện tại

Ví dụ sau đây chúng ta sẽ tích hợp stripe payment bằng cách áp dụng Service Object. Stripe service sẽ tạo các stripe customers dựa trên email address hay token.
Trước tiên cùng nhìn qua PaymentsController, ta có thể thấy controller rất đơn giản nhưng lại khá rõ ràng, mạch lạc do các xử lý đã được gói gọn trong Payment service.
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/1.png)

`payment_service.rb` được đặt trong `app/services` folder

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/2.png)

## 2. Views Objects (Presenter)
View objects cho phép đóng gói các logic liên quan đến việc hiển thị và đảm bảo cho hai thành phần models và views được rõ ràng. View không nên chứa các tính toán logic.
Để giải quyết các vấn đề logic ta có thể sử dụng rails helper, đối với các logic có độ phức tạp cao ta nên dùng Presenter. 
Giả sử ta có ví dụ hiển thị thông tin user như sau: 

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/3.png)

Dễ dàng nhận ra ta có thể kết hợp `first_name`, `last_name` để dùng về sau và code được tường mình hơn, cũng tương tự đối với việc đưa logic sử dụng class vào presenter

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/4.png)
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/5.png)

## 3. Query Object
Query Object là một kiểu design pattern cho phép chuyển các query logic từ Controllers và Models vào các lớp tái sử dụng (Reusable Classes).
Index controller dưới đây mô ta việc quert danh sách các bài Post có dạng "Video" và lớn hơn 100 lượt views.

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/6.png)

Vấn đề gặp phải ở đây là: 

 - Không thể tái sử dụng code
 - Khó khăn để test logic
 - Những thay đổi trong schema có thể làm đoạn code trên không còn đúng đắn

Ta sẽ refactor lại controller bằng cách sử dụng các scopes dưới đây:

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/7.png)

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/8.png)

Đến đây controller đã gọn gàng hơn rất nhiều rồi, nhưng với models thì chưa hẳn đã thực sự tối ưu. Ta đã tạo các scopes cho mỗi condition như vậy sẽ làm tăng lượng code trong model và sự kết hợp các scopes trong các trường hợp tương tự.

Rất đơn giản ta có thể giải quyết issue này bằng cách tạo thêm một Query Object: `video_query.rb`
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/9.png)

![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/10.png)

Bây giờ các query của chúng ta đã có thể dễ dàng tái sử dụng đối với các models có cấu trúc schema tương tự.
## 4. Decorators
Decorator là một design pattern cho phép bổ sung một behavior vào một object mà không ảnh hưởng đến behavior của các objects khác trong cùng một class. Sử dụng Decorator hữu ích trong việc cleaning up logic/code trong view và controller. Giả để thêm một user decorator ta sẽ thực hiện các bước sau:

 1. Tạo thư mục `app/decorator`
 2. Thêm `decorate` helper trong `ApplicationHelper`
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/11.png)
 3. Thêm `base_decorator.rb` vào thư mục `app/decorators` 
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/12.png)
 4. Tạo file user_decorator.rb trong `app/decorators`
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/13.png)
 5. Init `@user_decorator` ở controller
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/14.png)
 6. Sử dụng decorator đã init ở controller vào view
![enter image description here](https://www.bacancytechnology.com/blog/wp-content/uploads/2019/12/15.png)

Bài viết được tham khảo từ nguồn: 
 - [Refactoring a fat Rails Model](https://medium.com/@jaysadiq/refactoring-a-fat-rails-model-dc3cfda64d22)
 - [Design Patterns in Ruby On Rails](https://www.bacancytechnology.com/blog/design-patterns-in-ruby-on-rails)
