##Hello World

###Giới thiệu
Để bắt đầu bất kì một ngôn ngữ nào ta cũng cần phải có bài này. Nhưng ở đây ta làm việc với Message Queue nên ta chỉ đẩy chuỗi `Hello World` vào RabbitMQ rồi lấy ra và in lên màn hình.

###Môi trường

####Python 2.7

Để làm việc với RabitMQ ta cần có thư viện pika trên python 

Cài đặt pika bằng pip

`pip install pika`

####RabbitMQ-Server

Để nhanh chóng mình sẽ không cài đặt rabbitmq-server và chạy trong container dùng docker

```
docker run -d -p 5672:5672 rabbitmq

```

###Code

Ta cần 2 chương trình. Một dùng để gửi message vào RabbitMQ và một để lấy message ra rồi in lên màn hình

####Gửi vào RabbitMQ

```

#!/usr/bin/env python
import pika
connection = pika.BlockingConnection(pika.ConnectionParameters('172.17.0.2'))
channel = connection.channel()
channel.queue_declare(queue='hello')
channel.basic_publish(exchange='',routing_key='hello',body='Hello World!')
connection.close()

```
#### Giải thích một số câu lệnh
- dòng 1: khai báo thư viện pika

- dòng 2: tạo kết nối TCP tới RabbitMQ-server tại địa chỉ 172.17.0.2. Nếu rabbitmq-server của các bạn có địa chỉ khác và port khác thì phải sửa tại đây

- dòng 3: tạo một channel bên trong kết nối TCP vừa tạo

- dòng 4: Định nghĩa queue name là hello

- dòng 5: publish message queue vào rabbitmq-serer có body là nội dung message `Hello World!`

- dòng 6: đóng kết nối TCP
Kiểm tra queue có trong rabbit 

<img src="http://i.imgur.com/xrECTbv.png">

Như vậy là ta đã đẩy thành công message chứa chuỗi `Hello World!` vào trong RabbitMQ

####Lấy message từ RabbitMQ

```
#!/usr/bin/env python
import pika
connection=pika.BlockingConnection(pika.ConnectionParameters('172.17.0.2'))
channel=connection.channel()

def callback(ch,method, parameters,body):
	print "[-] Received: %r" %body

channel.basic_consume(callback,queue='hello',no_ack=True)
print "Receiving message queue\n"
channel.start_consuming()
connection.close()

```
##### Giải thích một số câu lệnh

- hàm callback để in ra chuỗi nhận được

- basic_consum(): subscribe với queue name  là `hello`

- start_consuming(): bắt đầu consume

Kiểm tra queue trong rabbitMQ 

<img src="http://i.imgur.com/jt342lh.png">

Ta thấy queue `hello` không còn message nào.



