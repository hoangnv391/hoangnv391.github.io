---
layout: post
title: Vector table và vai trò của nó trong xử lý ngắt (Interrupt)
categories: embedded interrupt
---
*(Tài liệu được viết dựa trên thao tác với ARM Cortex-M4)*

*Vector table là một khái niệm quan trọng trong ngắt. Trong bài viết này, hãy cùng mình tìm hiểu sơ lược về vector table.*
*Lưu ý: Trong bài viết sẽ có một số chỗ mình sẽ dùng tiếng Anh xen lẫn với tiếng Việt do chưa biết dịch chúng ra sao, mong các bạn thông cảm!!!.*

# I. Vector table là gì?
Vector table là một mảng của các data word nằm ở trong system memory, mỗi data word lưu trữ địa chỉ của một hàm ngắt. Trong ARM Cortex-M4, data word là một mảng bao gồm 4 byte liên tiếp nhau trên bộ nhớ, và địa chỉ của mỗi data word phải chia hết cho 4.
