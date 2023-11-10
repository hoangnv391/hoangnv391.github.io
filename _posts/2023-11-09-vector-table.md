---
layout: post
title: Vector table và vai trò của nó trong xử lý ngắt (Interrupt)
categories: embedded interrupt
---
*(Tài liệu được viết dựa trên thao tác với ARM Cortex-M4)*

*Vector table là một khái niệm quan trọng trong ngắt. Trong bài viết này, hãy cùng mình tìm hiểu sơ lược về vector table.*  
*Lưu ý: Trong bài viết sẽ có một số chỗ mình sẽ dùng tiếng Anh xen lẫn với tiếng Việt do chưa biết dịch chúng ra sao, mong các bạn thông cảm!.*

# I. Vector table là gì?
Vector table là một mảng của các data word nằm ở trong system memory, mỗi data word lưu trữ địa chỉ của một hàm ngắt. Trong ARM Cortex-M4, data word là một mảng bao gồm 4 byte liên tiếp nhau trên bộ nhớ, và địa chỉ của mỗi data word phải chia hết cho 4.  

Vector table có thể được đặt tại một vùng nhớ khác và việc đặt lại này được thực hiện bởi một thanh ghi nằm trong SCB là Vector table Offset Register (VTOR). Sau khi MCU được reset, giá trị của VTOR được đặt lại thành 0, do đó, vector table được đặt tại địa chỉ 0x0 sau khi reset. Hãy thử nhìn qua cấu trúc của một vector table:  

![_config.yml]({{ site.baseurl }}/images/vector-table/vector-table-structure.png)  

Ví dụ, nếu reset có exception type là 1, với việc mỗi data word có kích thước 4 byte, thì địa chỉ của reset vector là 1 x 4, tức là bằng 0x00000004, và NMI vector (type 2) được đặt tại địa chỉ 2 x 4 = 0x00000008. Data word tại địa chỉ 0x00000000 được sử dụng để lưu trữ giá trị khởi tạo của Main Stack Pointer (MSP).  

Bit có trọng số thấp nhất (Least Significant Bit - LSB) của mỗi exception vector chỉ định exception có được thực thi ở thumb state hay không. Từ khi Cortex-M processor chỉ hỗ trợ các thumb instruction, bit LSB của các exception vector phải được đặt thành 1. Vector table thường được định nghĩa trong start-up code, thứ được cung cấp bởi các nhà sản xuất MCU.  

# II. Tại sao vector table lại quan trọng trong xử lý ngắt?
Với việc lưu trữ giá trị khởi tạo của MSP và các địa chỉ hàm ngắt (ISR), vector table đương nhiên trở thành một phần quan trọng trong xử lý ngắt. Nếu địa chỉ của vector table, thứ được lưu trữ trong thanh ghi VTOR bị sai, hoặc địa chỉ của một ngắt được lưu trữ trong vector table bị sai, thì chương trình sẽ không thể hoạt động theo đúng mục đích của lập trình viên.  

Thêm vào đó, với việc thanh ghi VTOR - thanh ghi chứa địa chỉ của vector table, có để được cấu hình, việc một chương trình có nhiều hơn 1 vector table là hoàn toàn có thể và việc này được ứng dụng với một số mục đích trong thực tế.  

# III. Ứng dụng của việc có thể có nhiều hơn 1 vector table là gì?
## 1. Thiết bị sử dụng boot loader
Trong một vài vi điều khiển, có thể có nhiều bộ nhớ để lưu chương trình: boot ROM và user flash memory. Boot loader thường được lập trình trước và được lưu vào boot ROM bởi nhà sản xuất MCU. Khi MCU khởi động, nó sẽ thực thi boot loader ở trong boot ROM trước tiên, sau đó chuyển tới thực thi chương trình của người dùng trong user flash, VTOR được lập trình để trỏ tới địa chỉ bắt đầu của user flash memory, do đó, vector table trong user flash sẽ được sử dụng.  

![_config.yml]({{ site.baseurl }}/images/interrupt.png)  

## 2. Chương trình được tải vào trong RAM
Trong một vài trường hợp, chương trình có thể được tải từ một vài nguồn bên ngoài vào trong RAM để thực thi. Nó có thể được lưu trữ trên SD card, hay thậm chí là có thể được truyền tải trên đường truyền mạng.  

Trong trường hợp này, chương trình được lưu trong chip cần khởi tạo một vài phần cứng, sao chép chương trình được lưu ở bên ngoài lên RAM, cập nhật giá trị thanh ghi VTOR và sau đó, thực thi chương trình bên ngoài đã được lưu trên RAM.  
![_config.yml]({{ site.baseurl }}/images/interrupt.png)  

## 3. Vector table không cố định
Trong một vài trường hợp, lập trình viên muốn có nhiều impletation của cùng một ISR trong ROM và chuyển đổi giữa chúng vào các giai đoạn khác nhau của chương trình. Trong trường hợp này, họ có thể sao chép vector table từ program memory vào SRAM và lập trình VTOR để trỏ tới vector table trên SRAM. Từ khi nội dung trên SRAM có thể được thay đổi bất cứ khi nào, lập trình viên có thể sửa đổi interrupt vector một cách dễ dàng ở các giai đoạn khác nhau của chương trình.  

Trong một cấu hình tối thiểu, vector table cần cung cấp giá trị khởi tạo của MSP vàn reset vector để hệ thống có thể khởi động. Thêm vào đó, tùy thuộc vào chương trình mà lập trình viên cần cung cấp NMI vector, bởi một vài thiết bị có thể có NMI triggered ngay sau khi khởi động, và HardFault vector để xử lý lỗi.  

# IV. Tổng kết
Trên đây là một vài kiến thức mà mình có được về vector table, rất mong nó có thể giúp ích một phần nào đó cho các bạn. Hẹn gặp lại các bạn ở các bài viết sau.
