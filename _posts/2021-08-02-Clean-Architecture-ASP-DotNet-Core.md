---
layout: post
title: Clean Architecture với ASP.NET Core qua ví dụ.
categories: .NET
---
*Dịch từ bài viết [Clean Architecture for ASP.NET Core Solution: A Case Study](https://blog.ndepend.com/clean-architecture-for-asp-net-core-solution/?fbclid=IwAR0PATLqQNAC9nQl48y3Do0wnwzuD2nuPyF7prguoX87NrD_4a08hJNAJXs)*

Trong bài viết này, tôi sẽ khám phá mẫu giải pháp Clean Architecture .NET của Jason Taylor, code mẫu có sẵn tại [link github này](https://github.com/jasontaylordev/CleanArchitecture) . Đó là một khuôn mẫu (template) tốt, dựa trên một số thông lệ tốt đang được chấp nhận rộng rãi trong ngành công nghiệp hiện tại.

Trước khi bắt đầu, hãy nhấn mạnh lý do tại sao việc quan tâm đến kiến trúc của giải pháp (phần mềm) lại quan trọng. Đây là một trích dẫn từ cuốn Clean Architecture của Uncle Bob, trang 137:

> *The primary purpose of architecture is to support the life cycle of the system. Good architecture makes the system easy to understand, easy to develop, easy to maintain, and easy to deploy. The ultimate goal is to minimize the lifetime cost of the system and to maximize programmer productivity.*

> *Mục đích chính của kiến trúc là hỗ trợ vòng đời của hệ thống. Kiến trúc tốt làm cho hệ thống dễ hiểu, dễ phát triển, dễ bảo trì và dễ triển khai. Mục tiêu cuối cùng là giảm thiểu chi phí lâu dài của hệ thống và tối đa hóa năng suất của lập trình viên*
