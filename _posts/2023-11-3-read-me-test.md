---
layout: post
title: Ngoại lệ và ngắt trong lập trình nhúng
categories: embedded
---

*Ngoại lệ và ngắt là một phần rất quan trọng trong lập trình nhúng. Trong bài viết này, hãy cùng mình tìm hiểu sơ lược về chúng.*    
*(Tài liệu được viết dựa trên thao tác với ARM Cortex-M4)*    
*Các từ viết tắt được sử dụng trong bài viết*    
| Month    | Savings |
| -------- | ------- |
| January  | $250    |
| February | $80     |
| March    | $420    |

![_config.yml]({{ site.baseurl }}/images/interrupt.png)
# I.Tổng quan
## 1. Ngoại lệ và ngắt (Exception and Interrupt)
Trong bài viết, mình sẽ chỉ sử dụng từ "ngắt", "ngoại lệ" - với bản chất giống như ngắt, chỉ là có một chút khác biệt, mình sẽ giải thích sự khác biệt đó ở phía dưới.  

Ngắt thường được tạo ra bởi phần cứng, như các ngoại vi hoặc các chân đầu vào của GPIO, trong một số trường hợp, ngắt cũng có thể được tạo ra bởi phần mềm. Trong lập trình nhúng, nếu muốn xử lý một sự kiện ngắt thì cần có một hàm riêng để xử lý ngắt, hàm đó còn được gọi là Interrupt Service Routines (ISR).  

Vậy còn ngoại lệ thì sao?. Giống như ngắt, ngoại lệ cũng được tạo ra bởi phần cứng, chỉ có khác biệt là, phần cứng ở đây chính là bộ xử lý. Ngoại lệ thường xảy ra khi chương trình gặp lỗi về logic toán học (ví dụ: chia cho 0, sẽ gây ra rỗi MemFault) hoặc khi cấu hình sai ở những phần cứng mà bộ xử lý muốn truy cập (ví dụ: khi chưa cấp clock cho một ngoại vi, mà bộ xử lý cố truy cập vào các thanh ghi ở ngoại vi đó, sẽ dẫn đến lỗi transfer error, là một dạng của BusFault).  

## 2. Nested Vector Interrupt Controller (NVIC)
NVIC là một ngoại vi nằm trong bộ xử lý, được sử dụng để xử lý các ngoại lệ và ngắt, bao gồm cấu hình, độ ưu tiên và interrupt masking.  
NVIC có thể xử lý 2 nguồn ngắt:  
* Pulsed interrupt request - yêu cầu ngắt chỉ xảy ra trong một clock cycle. Khi NVIC nhận được tín hiệu ngắt, trạng thái chờ tương ứng sẽ được đặt và giữ cho đến khi ngắt được phục vụ.
* Level triggred interrupt request - nguồn ngắt sẽ giữ request high cho đến khi ngắt được phục vụ.

# II. Ngắt và quá trình xử lý ngắt
| Month    | Savings |
| -------- | ------- |
| January  | $250    |
| February | $80     |
| March    | $420    |


