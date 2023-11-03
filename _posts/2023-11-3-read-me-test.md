---
layout: post
title: Sơ lược về ngoại lệ và ngắt trong lập trình nhúng
categories: embedded interrupt
---

*Ngoại lệ và ngắt là một phần rất quan trọng trong lập trình nhúng. Trong bài viết này, hãy cùng mình tìm hiểu sơ lược về chúng.*    
*(Tài liệu được viết dựa trên thao tác với ARM Cortex-M4)*    
*Các từ viết tắt được sử dụng trong bài viết*    

| **Từ viết tắt**      | **Viết đầy đủ** |
| :-----------: | :-----------: |
| ISR      | Interrupt Service Routine       |
| NVIC   | Nested Vector Interrupt Controller        |
| NMI | Non-masked Interrupt |
| SCB | System Control Block |
| STIR | Software Trigger Interrupt Register |


![_config.yml]({{ site.baseurl }}/images/interrupt.png)
# I.Tổng quan
## 1. Ngoại lệ và ngắt (Exception and Interrupt)
Ngắt thường được tạo ra bởi phần cứng, như các ngoại vi hoặc các chân đầu vào của GPIO, trong một số trường hợp, ngắt cũng có thể được tạo ra bởi phần mềm. Trong lập trình nhúng, nếu muốn xử lý một sự kiện ngắt thì cần có một hàm riêng để xử lý ngắt, hàm đó còn được gọi là Interrupt Service Routines (ISR).   

## 2. Nested Vector Interrupt Controller (NVIC)
NVIC là một ngoại vi nằm trong bộ xử lý, được sử dụng để xử lý các ngoại lệ và ngắt, bao gồm cấu hình, độ ưu tiên và interrupt masking.  

NVIC có thể xử lý 2 nguồn ngắt:  
* Pulsed interrupt request - yêu cầu ngắt chỉ xảy ra trong một clock cycle. Khi NVIC nhận được tín hiệu ngắt, trạng thái chờ tương ứng sẽ được đặt và giữ cho đến khi ngắt được phục vụ.
* Level triggred interrupt request - nguồn ngắt sẽ giữ request high cho đến khi ngắt được phục vụ.

# II. Ngắt và quá trình xử lý ngắt
## 1. Tổng quan
Ngắt là một sự kiện thường được tạo ra bỏi phần cứng, làm thay đổi luồng chạy thông thường của chương trình. Khi một ngoại vi hoặc phần cứng cần được phục vụ bởi bộ xử lý, quy trình thường sẽ xảy ra như sau:
1. Ngoại vi gửi một yêu cầu xử lý ngắt (interrupt request) tới bộ xử lý.
2. Bộ xử lý hoãn thực thi tác vụ hiện tại.
3. Bộ xử lý thực thi ISR để phục vụ ngoại vi hoặc phần cứng, việc xóa interrupt request trong ISR cần được thực hiện nếu cần thiết.
4. Bộ xử lý tiếp tục thực hiện công việc trước khi ngắt xảy ra.

## 2. Phân loại ngắt và mức độ ưu tiên
Xét theo exception number, có 2 kiểu ngắt:
* 1 - 15 là ngoại lệ hệ thống (system exception)
* Từ 16 trở lên là các ngắt (interrupt)

Khi 2 ngắt cùng xảy ra, việc thực thi ISR của ngắt nào trước được quyết định bởi mức độ ưu tiên, ngắt nào có mức độ ưu tiên cao hơn (priority level nhỏ hơn) sẽ được ưu tiên thực thi trước. Phần lớn độ ưu tiên của các ngắt (priority) là có thể cấu hình được. Tuy vậy, có một vài ngắt mà mức độ ưu tiên của chúng là cố định, bao gồm:
* Reset Handler - (-3)
* Non-Masked Interrupt (NMI) - (-2)
* HardFault - (-1)

Priority level của các ngắt này là các số âm, thể hiện rằng chúng có mức độ ưu tiên cao hơn tất cả các ngắt còn lại.  

*Hãy nhớ, exception number là duy nhất cho mỗi ngắt.*

## 3. Tổng quan về quản lý ngắt
Tất cả các thanh ghi trong SCB và NVIC chỉ có thể được truy cập trong Priviledged Mode. Tuy nhiên, thanh ghi STIR trong NVIC có thể được cấu hình để cho phép truy cập trong chế độ Unpriviledged Mode.  

Sau khi reset, mọi ngắt bị vô hiệu hóa và mọi mức ưu tiên được đặt hết về 0. Trước khi sử dụng bất kỳ ngắt nào, cần phải:
* Cấu hình mức độ ưu tiên cho ngắt (đây là một tùy chọn).
* Kích hoạt interrupt generation trong ngoại vi, thứ sẽ kích hoạt ngắt.
* Kích hoạt ngắt trong NVIC.

Khi một ngắt được kích hoạt, ISR tương ứng sẽ được thực thi. Tên của ISR có thể được tìm thấy trong startup code, thứ được cung cấp bởi nhà sản xuất (vendor). Tên của ISR sau khi được viết lại cần phải giống với tên được sử dụng trong vector table để linker có thể thay thế địa chỉ của hàm ngắt vào vector table một cách chính xác.  

## 4. Định nghĩa về độ ưu tiên.
Việc ngắt có thể được thực thi hay không phụ thuộc vào mức độ ưu tiên của ngắt cần được phục vụ và độ ưu tiên hiện tại của bộ xử lý. Một ngắt có mức độ ưu tiên cao hơn (có số priority level nhỏ hơn) có thể chiếm quyền một ngắt có mức độ ưu tiên thấp hơn (có số priority level lớn hơn), đây được gọi là ngắt lồng (nested exception/interrupt).  

Một vài ngắt như Reset, NMI và HardFault có mức ưu tiên là cố định. Chúng có priority level là số âm, thể hiện rằng chúng có mức ưu tiên cao hơn tất cả các ngắt khác. Các ngắt khác có mức độ ưu tiên có thể cấu hình được từ 0 - 255.  

Tuy có thể xử lý tới 256 priority level, trong thực tế, con số này thấp hơn trên các sản phầm, thường là 8, 16, 32 và hơn nữa. Điều này là vởi vì nếu có quá nhiều mức ưu tiên ngắt, điều đó sẽ làm tăng mức tiêu thụ năng lượng và làm giảm tốc độ. Trong phần lớn trường hợp, các ứng dụng thực tế chỉ yêu cầu một lượng nhỏ các ngắt có mức độ ưu tiên có thể cấu hình được. Việc này thường được thực hiện bằng cách cắt bớt các bit có trọng số thấp (LSB) trong thanh ghi Priority Configuration Register (PCR).  

## 5. Các trạng thái của ngắt
Trong phần này, mình sẽ sử dụng các từ tiếng Anh để mô tả trạng thái của một ngắt, bởi dịch sang tiếng Việt hơi khó.  
Các trạng thái có thể có của một ngắt:  
* Disabled or enabled
* Pending
* Active
* Pending + Active

Sự kết hợp các trạng thái của ngắt là khả thi. Ví dụ, trong khi một ngắt đang được xử lý (active), có thể disable nó và khi một yêu cầu ngắt đến từ cùng ngoại vi trước đó trước khi ra khỏi ngắt, sẽ dẫn đến việc ngắt bị disabled trong khi đang được active cùng với trạng thái chờ (pending status).  

Một ngắt có thể được chấp nhận bởi bộ xử lý nếu:
* Pending status is set.
* Interrupt is enabled.
* Mức ưu tiên của ngắt cao hơn ngắt hiện tại.

# III. Tổng kết
Trên đây là những tóm lược của mình về ngoại lệ và ngắt trong lập trình nhúng, rất hi vọng những kiến thức này có thể giúp một phần nào đó cho các bạn.  
Mình sẽ còn có thêm nhiều bài viết khác về ngắt bởi bài viết này mới chỉ là sơ lược về ngắt, các bạn hãy tìm đọc trong mục "interrupt" để có thể có thêm kiến thức nhé :3 !!!
