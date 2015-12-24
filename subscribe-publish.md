##Exchange

Ý tưởng chính trong rabbitmq model là producer ko gửi trực tiếp message tới rabbitmq.
Thay vì đó producer chỉ gửi message tới exchange. Một exchange là một thứ rất đơn giản. Một bên nó nhận message từ những producers. Một bên đẩy chúng tới queues. Exchange phải biết chính xác làm gì với message mà nó nhận. 

Có một só các kiểu của exchange là:
- direct
- topic
- headers
- fanout

###fanout

```
channel.exchange_declare(exchange='logs',type='fanout')

```

- fanout exchange rất đơn giản. Nó chỉ quảng báo tất cả các message nó nhận được tới tất cả các queue mà nó biết. 

- Để  publish message vào exchange này ta cần khai báo trong tham số exchange của basic_publish

```
channel.basic_publish(exchange='logs',routing_key='',body=message)

```

- Ở bài trước ta có khai báo publish message vào queue mà không khai báo exchange. Mặc định nếu exchange='' thì message sẽ được route tới queue với tên được định nghĩa trong mục routing_key nếu nó tồn tại

```

channel.basic_publish(exchange='',routing_key='hello',body=message)

```



##Queue tạm

- Ở bài trước để subscribe vào một queue chúng ta consumer phải biết tên của queue đó.
- Nhưng ta cũng có thể để rabbitmq tự tạo cho ta một tên của queue khi ko khai báo tham số queue

```
result= channel.queue_declare()

```

- kết quả trả về tại result.method.queue là tên của queue được định nghĩa

<img src="http://i.imgur.com/Yg7Snom.png">


- Khi ta muốn xóa queue sau khi consumer ngắt kết nối ta cần khai báo tham số exclusive về True như sau

```
result = channel.queue_declare(exclusive=True)

```

##Binding

Chúng ta đã tạo exchange kiểu fanout. Bây giờ chúng ta cần nói với exchange rằng hãy gửi message tới queue của tôi. Đó là mối quan hệ giữa exchange và queue được gọi là binding


```
channel.queue_bind(exchange='logs',queue=result.method.queue)

```

và ta kiểm tra `rabbitmqctl list_bindings`

<img src="http://i.imgur.com/qEp5dzy.png">

##Code

Mục tiêu tạo một exchange là logs và một consumer định nghĩa một queue được rabbitmq tự đặt tên. Khi producer đẩy message vào rabbit thì exchange sẽ đẩu message vào queue mà consumer định nghĩa

### producer.py

```
#!/usr/bin/env python
import pika
import sys
connection = pika.BlockingConnection(pika.ConnectionParameters('172.17.0.2'))
channel = connection.channel()

channel.exchange_declare(exchange='logs',type='fanout')

message = ' '.join(sys.argv[1:]) or 'Hello World!'

channel.basic_publish(exchange='logs',routing_key='',body=message)
connection.close()

```


### consumer.py

```
#!/usr/bin/env python
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters('172.17.0.2'))
channel = connection.channel()

result = channel.declare_queue(exclusive=True)
queue_name = result.method.queue
def callback(ch,method,parameters,body):
	print "[+] Received: %r" %body
channel.basic_consume(exchange='logs',queue=queue_name,no_ack=True)
print "[-]Waiting for message"
channel.start_consuming()

```

### Run

- Ta sẽ mở 2 tab terminal để chạy 2 code một lúc.. 
- Chạy consumer.py `python consumer.py`
- Chạy producer và đẩy message vào `python producer.py Test`

<img src="http://i.imgur.com/4zFCWlu.png">

