---
layout: post
title: Ngoại lệ và ngắt trong lập trình nhúng
categories: embedded
---

*Ngoại lệ và ngắt là một phần rất quan trọng trong lập trình nhúng. Trong bài viết này, hãy cùng mình tìm hiểu sơ lược về chúng.*    
*(Tài liệu được viết dựa trên thao tác với ARM Cortex-M4)*    
*Các từ viết tắt được sử dụng trong bài viết*    

| **Từ viết tắt**      | **Viết đầy đủ** |
| :-----------: | :----------- |
| ISR      | Interrupt Service Routine       |
| NVIC   | Nested Vector Interrupt Controller        |



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

*Hãy nhớ, exception number là duy nhất cho mỗi ngắt*



