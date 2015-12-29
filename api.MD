### Web UI

- Rabbitmq có rất nhiều extension để người quản trị, người phát triển có thể làm việc dễ dàng với nó hơn. Một trong những extension đó , là Plugin management. 
- Khi enable plugin này ta sẽ có một Web ui http dựa trên API để quản lý và monitor rabbitmq server. 
- Bên cạnh đó cũng sẽ cung cấp cho ta thêm một command line tool đó là `rabbitmqadmin`
- Với plugin này ta có thể 
	+ Declare, list and delete exchanges, queues, bindings, users, virtual hosts and permissions.
	+ Monitor queue length, message rates globally and per channel, data rates per connection, etc
	+ Send and receive messages.
	+ Monitor Erlang processes, file descriptors, memory use.
	+ Export / import object definitions to JSON.
	+ Force close connections, purge queue
- plugin management đi kèm với rabbitmq-server khi cài đặt. Do vậy ta chỉ việc enable plugin này lên bằn câu lệnh sau

`rabbitmq-plugins enable rabbitmq_management`

- Truy cập vào web tại địa chỉ  `http://server-name:15672`
- Ta sử dụng tài khoản admin để đăng nhập vào trang quản trị (tài khoản có tags administration)
- Giao diện web của chương trình
<img src="http://www.rabbitmq.com/img/management/overview.png">

- Và từ đây sẽ cung cấp cho ta HTTP Restfull API người dùng có thể phát triển cho ứng dụng của họ.
- Để tìm hiểu thêm về tài liệu API truy cập tại đây [API Rabbit](https://raw.githack.com/rabbitmq/rabbitmq-management/rabbitmq_v3_6_0/priv/www/api/index.html)

### API 

- Phần này mình sẽ demo tương tác với rabbitmq thông qua api của plugin management đã được enable ở trên
- Ngôn ngữ sử dụng: Python
- Thư viện cần thiết: httplib, urlib, json
- Mục đích: Hiển thị các user và trạng thái của  queue thông qua API

- Hiển thị user

```
import json,httplib,urllib,base64,sys


if len(sys.argv) < 3:
	print "USAGE: python user_list.py host:port user passwd"
	sys.exit(0)

host,port = sys.argv[1].split(':')
username = sys.argv[2]
password = sys.argv[3]


path = '/api/users'
method = 'GET'

conn = httplib.HTTPConnection(host,int(port))
creds  = base64.b64encode("%s:%s"  %(username,password))
header = {"Content-type":"application/json","Authorization":"Basic "+creds}
conn.request(method,path,"",header)
response = conn.getresponse()

#print response.read()
if response.status > 299:
	print "Error excuting API Call (%d): %s" %(response.status,response.read())
	system.exit(0)
payload = json.loads(response.read())
print '-'*100
print "\t\tUsername  \t\tPassword \t\t\t\t Tags"
print '-'*100
for i in range(len(payload)):
	print "\t\t"+str(payload[i]['name']) + "\t\t"+str(payload[i]['password_hash']) + "\t\t\t"+str(payload[i]['tags'])
	print '-'*100

sys.exit(0)

```

- Chạy chương trình trên ta sẽ có kết quả như sau

- Hiển thị trạng thái của queue

```
import sys,json,httplib,urllib,base64
def main():
	if len(sys.argv) < 6:
		print "USAGE queue_stats.py servername:port auth_user auth_passwd VHOST QUEUE_NAME"
		sys.exit(1)
	server,port = sys.argv[1].split(':')
	username = sys.argv[2]
	password = sys.argv[3]
	vhost = sys.argv[4]
	queue_name = sys.argv[5]

	vhost = urllib.quote(vhost,safe='')
	queue_name = urllib.quote(queue_name,safe='')
	path = '/api/queues/%s/%s' %(vhost,queue_name)

	method = 'GET'

	conn = httplib.HTTPConnection(server,int(port))
	credentials = base64.b64encode('%s:%s' %(username,password))

	header = { "Content-Type":"application/json",
				"Authorization":"Basic "+credentials}
	conn.request(method,path,"",header)
	response = conn.getresponse()
	if response.status > 299:
		print "Erorr Excuting API Call (%d): %s "%(response.status,response.read())
		sys.exit(0)
	#print response.read()
	payload = json.loads(response.read())
	#print payload
	print "\t Memory Used(bytes): "+str(payload['memory'])
	print "\t Consumers Count: "+str(payload['consumers'])
	print "\t Messages: "
	print "\t\t Unack: "+str(payload["messages_unacknowledged"])
	print "\t\t Ready: "+str(payload["messages_ready"])
	print "\t\t Total: "+str(payload["messages"])
	print "\t Node: " + str(payload["node"])
	print "\t Durable: "+str(payload["durable"])
	sys.exit(0)
if __name__=='__main__':
	main()

```


