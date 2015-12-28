##direct
- Khi một queue được định nghĩa nó sẽ tự động được bound tới exchange này sử dụng queue name như routing key. 

<img src="http://i.imgur.com/45umh9d.png">

##fanout
<img src="http://i.imgur.com/1J5ka8f.png">

- Như trên ảnh ta thấy exchange này sẽ multicast tới những consumer bound tới queue. Có nghĩa là khi ta gửi một message tới exchange này thì nó sẽ được phân phát tới tất cả các queue được attach tới exchange đó.

##topic
<img src="http://i.imgur.com/1J5ka8f.png">

Tương tự như direct nhưng nó mở rộng thêm.
- Ví dụ tôi muốn bind các log có các trường là ERROR hoặc WARNING thì ta phải dùng topic

##headers
