---
layout: post
title: "Clean Architecture với ASP.NET Core qua ví dụ."
date: 2021-08-02
categories: [.net]
---
*Dịch từ bài viết [Clean Architecture for ASP.NET Core Solution: A Case Study](https://blog.ndepend.com/clean-architecture-for-asp-net-core-solution/?fbclid=IwAR0PATLqQNAC9nQl48y3Do0wnwzuD2nuPyF7prguoX87NrD_4a08hJNAJXs)*

Trong bài viết này, tôi sẽ khám phá mẫu giải pháp Clean Architecture .NET của Jason Taylor, code mẫu có sẵn tại [link github này](https://github.com/jasontaylordev/CleanArchitecture). Đó là một khuôn mẫu (template) tốt, dựa trên một số thông lệ tốt đang được chấp nhận rộng rãi trong ngành công nghiệp hiện tại.

Trước khi bắt đầu, hãy nhấn mạnh lý do tại sao việc quan tâm đến kiến trúc của giải pháp (phần mềm) lại quan trọng. Đây là một trích dẫn từ cuốn Clean Architecture của Uncle Bob, trang 137:

> *The primary purpose of architecture is to support the life cycle of the system. Good architecture makes the system easy to understand, easy to develop, easy to maintain, and easy to deploy. The ultimate goal is to minimize the lifetime cost of the system and to maximize programmer productivity.*

> *Mục đích chính của kiến trúc là hỗ trợ vòng đời của hệ thống. Kiến trúc tốt làm cho hệ thống dễ hiểu, dễ phát triển, dễ bảo trì và dễ triển khai. Mục tiêu cuối cùng là giảm thiểu chi phí lâu dài của hệ thống và tối đa hóa năng suất của lập trình viên*  

## Cấu trúc Solution.

Đây là cấu trúc solution của Jason Taylor trên trên Visual Studio.  
Code của ứng dụng và code kiểm thử được phân tách rõ ràng trong hai thư mục tương ứng là **src** và **tests**. Đây là một điều kiện tiên quyết được khuyến khích với mọi dự án: Code ứng dụng và code kiểm thử không được nằm chung một chỗ!  

Phần bên dưới bao gồm cả kiểm thử (test), nhưng chúng tôi chủ yếu tập trung vào 4 project trong thư mục **src**  

![alt](https://blog.ndepend.com/wp-content/uploads/Net-Solution-Structure-Explorer.png)  

## Xem xét cấu trúc dưới dạng minh họa phụ thuộc.  

Biểu đồ [NDepend dependency graph](https://www.ndepend.com/docs/visual-studio-dependency-graph?_ga=2.79885010.477114683.1627913188-878189054.1627913188) bên dưới cho biết nhiều hơn so với Visual Studio. Từ biểu đồ này, bạn có thể sử dụng nhiều tính năng để bắt đầu khám phá sâu hơn.  

*Chú ý cách biểu thị màu sắc: Màu cam có nghĩa là project được chọn, màu xanh lá cây có nghĩa là lời gọi (caller) của project được chọn và xanh nước biển là được gọi bởi project được chọn*  

![alt](https://blog.ndepend.com/wp-content/uploads/Net-Solution-Structure-Graph.png)  

Bây giờ, chúng ta đã có một sơ đồ rõ ràng về kiến trúc của Solution. Sẽ thật hợp lý khi trình bày các project từ cấp thấp đến cấp cao theo thứ tự sau: *Domain* -> *Application* -> *Infrastructure* và *WebUI*. 


## Project, Component và Kiến trúc Nguyên khối (Monolithic Achitecture)

Có nhiều cách tổ chức cấu trúc của một ứng dụng ASP.NET Core. Ít project không hẳn là xấu, và nhiều project cũng không hẳn là tốt. Cần phải nhớ rằng: mỗi project sẽ được biên dịch thành một file dll, tức là độ chi tiết của mỗi project sẽ mang tính vật lý.  

Mặt khác, khái niệm Component lại mang tính logic. Một *Component* là một nhóm các kiểu có sự gắn kết với nhau (các class/interface/struture/enumerations), hoạt động cùng nhau để đạt được một mục tiêu cụ thể, rõ ràng. Chúng ta sẽ sử dụng các Component để cấu trúc các dự án thành các đơn vị, thành phần nhỏ:  

* Thành phần xử lý (unit of processing)   
* Thành phần phát triển (unit of development)   
* Thành phần để tái sử dụng (unit of re-use)   
* Thành phần kiểm thử (unit of test)   
* ..........................   

Chúng ta cần các thành phần này để triển khai một ứng dụng phức tạp: đây là hệ quả trực tiếp từ ý tưởng nổi tiếng của Descarte: Chia để trị - [Discourse of Method’s divide and conquers](https://en.wikipedia.org/wiki/Discourse_on_the_Method)   

Khái niệm Thành phần (component) mang tính chi tiết hơn khái niệm Project, do đó, một project thường chứa một số các component. Cá nhân tôi thích sử dụng một namespace để cấu trúc một project thành các component: *Application.NamespaceA* triển khai component A, *Application.NamespaceB* triển khai component B...   

Nếu có sự phụ thuộc lẫn nhau giữa các component (Component A phụ thuộc vào component B và ngược lại, chẳng hạn), các thành phần này sẽ không còn là các đơn vị riêng biệt nữa: chúng sẽ không thể được phát triển, tái sử dụng, kiểm thử...một cách độc lập. Và vì thế, chúng ta sẽ có một **Siêu thành phần - Super Component**, biến ứng dụng của chúng ta thành một kiến trúc nguyên khối. Trong lập trình, điều này được gọi với bằng một từ mang nghĩa không tốt: **spaghetti code**. Đó chính là lý do vì sao chúng ta cần phân lớp các component (không có sự phụ thuộc lẫn nhau). Nếu bạn sử dụng namespace để gọi các component khi sử dụng, bạn có thể chạy các quy tắc code như Avoid namespace mutually dependent (tránh các namespace phụ thuộc lẫn nhau) để ngăn cái gọi là spaghetti code.   

Do đó, một số đặc điểm chính của một **Clean Architecture** là:
* Các project được sử dụng nhưng các thành phần vật lý độc lập. Ví dụ, có thể tách phần code core của ứng dụng ASP.NET Core riêng biệt với phần code giúp truy cập vào database.
* Các component được sử dụng để cấu trúc code thành các đơn vị nhỏ (chia để trị)
* Không có sự phụ thuộc lẫn nhau giữa các component: chúng phải được phân lớp.   

Tôi không chắc liệu Jason có ý định sử dụng namespace để xác định các component của project hay không, nhưng cấu trúc namespace của project gần như được phân lớp hoàn toàn như được thể hiện bằng ma trận dưới đây:  
![alt](https://blog.ndepend.com/wp-content/uploads/Clean-Architecture-Layered-Components.png)   

*Hình chữ nhật màu đỏ thể hiện một chút vướng víu trong namespace CleanArchitecture.Application.Common và namespace Enties/Event. Hình dưới đây cung cấp một số gợi ý về những việc cần làm để có được một kiến trúc phân lớp hoàn thiện*   
![alt](https://blog.ndepend.com/wp-content/uploads/Avoid-Namespaces-Mutually-Dependents-Code-Rule.png)   

## Domain   

Như đã nói cụ thể trong trang github của CleanArchitecture, project Domain bao gồm các entity(thực thể), enums, exception, interface, kiểu và code logic cụ thể cho lớp(layer) Domain. Điều này là kết quả của việc áp dụng DDD (Domain Driven Design), rất phổ biến trong cấu trúc phần mềm. 