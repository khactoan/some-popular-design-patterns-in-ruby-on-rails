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


Bài viết được tham khảo từ nguồn: 
 - [Refactoring a fat Rails Model](https://medium.com/@jaysadiq/refactoring-a-fat-rails-model-dc3cfda64d22)
 - [Design Patterns in Ruby On Rails](https://www.bacancytechnology.com/blog/design-patterns-in-ruby-on-rails)
