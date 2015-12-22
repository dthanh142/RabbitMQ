# RabbitMQ
##1. Khái niệm
RabbitMQ là một message broker. ý tưởng chính khá đơn giản, nó accept và forward message. Có thể nghĩ nó như là một post office và khi bạn gửi thư tới post box bạn khá chắc chắn rằng Postman sẽ vận chuyển thư tới người nhận. Sử dụng phép ẩn dụ này thì RabbitMQ là post office , postman hoặc post box.

Vấn đề chính mà RabbitMQ khác với post office là nó ko vận chuyển thư bằng giấy mà thay vì đó nó accept, store và forward message binary.

####Producer

Cũng có nghĩa tương đương bên gửi

####Consumer

Cũng có nghĩa tương đương bên nhận

####Queue

Là tên của mailbox. Nó sống bên trong RabbitMQ. Nhiều producer có thể gửi message vào một queue Hoặc nhiều consumer có thể lấ message từ một queue
