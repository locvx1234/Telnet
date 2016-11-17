## Cấu hình Router cho phép Telnet

<img src="http://i.imgur.com/7JtK7Xq.png">

Để cấu hình Router ta phải sử dụng nối dây console từ máy tính vào cổng console trên Router. 

Tuy nhiên, với những máy tính ở xa, ta có thể sử dụng chính kết nối Internet để cấu hình cho Router thông qua Telnet.

Telnet là một giao thức tầng application, sử dụng kết nối truyền tin tin cậy TCP với port 23 cho phép kết nối từ xa từ một máy tính vào Router để thực hiện cấu hình.

Để sử dụng dịch vụ này thì máy tính phải có kết nối với Router và Router phải cho phép telnet vào mình.

Tức là, Router phải mở cổng VTY - 1 cổng đặc biệt cho các thực thể truy cập từ xa vào Router.

Thông thường các dòng IOS Cisco có 5 đường VTY 0-4, ta tiến hành đặt password cho 5 đường này.

Trên Router0 ta cấu hình như sau:
 
	Router> 
	Router>enable
	Router#conf t
	Router(config)#line vty 0 4
	Router(config-line)#password locvx1234
	Router(config-line)#login
	Router(config-line)#exit
	Router(config)#

Telnet đòi hỏi cả password enable, ta cấu hình như sau : 

	Router> 
	Router>enable
	Router#conf t
	Router(config)#enable secret locspass
	Router(config)#exit
		
Sau khi đặt password cho đường vty và password enable, ta có thể telnet từ PC có kết nối với nó (ping đến được Router đó)

<img src="http://i.imgur.com/K5xlvna.png">

