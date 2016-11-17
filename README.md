<a name="#top"></a>
# Telnet-
ghi chép về Telnet 


## Mục lục 

[1. Vài nét về Telnet](#info)

[2. Cài đặt và cấu hình](#install)

[3. Bắt gói tin TELNET](#wireshark)

[4. Tham khảo](#reference)

<img src="http://i.imgur.com/aH6bCMb.gif">


<a name="info"></a>
### 1. Vài nét về Telnet

Telnet (TErminaL NETwork) là một giao thức theo mô hình client-server dùng để kết nối tới các máy tính ở xa dựa trên cơ sở TCP/IP.

Port : 23 

Phiên giao dịch Telnet sử dụng đường truyền không mã hóa (unencrypted). Vì thế, thông tin dễ bị đánh cắp trên đường truyền.

<img src="http://i.imgur.com/SaylC05.png">


Một giao thức khác an toàn, bảo mật hơn Telnet là [SSH](https://github.com/locvx1234/SSH)


<a name="install"></a>
###2. Cài đặt và cấu hình

##### Phía Server

Mình chọn cài đặt Telnet trên Server : CentOS 7

	# yum install telnet telnet-server -y
	
Sau khi gói `telnet` và `telnet-server` đã cài đặt xong, bạn start nó lên :

	# systemctl start telnet.socket
	# systemctl enable telnet.socket
	
Cho phép Telnet qua Firewall :

	# firewall-cmd --permanent --add-port=23/tcp
	# firewall-cmd --reload
	
Với CentOS 6 thì phức tạp hơn : 

	# yum install telnet telnet-server -y
	
Sửa file cấu hình của telnet : 
	
	# cp /etc/xinetd.d/telnet /etc/xinetd.d/telnet.orig        # backup file 
	# vi /etc/xinetd.d/telnet

Đặt tham số `disable = no`


	# default: on
	# description: The telnet server serves telnet sessions; it uses \
	#       unencrypted username/password pairs for authentication.
	service telnet
	{
			flags           = REUSE
			socket_type     = stream
			wait            = no
			user            = root
			server          = /usr/sbin/in.telnetd
			log_on_failure  += USERID
			disable         = no
	}

Bật dịch vụ :

	# service xinetd start
	
Tự động start khi khởi động : 

	# chkconfig telnet on
	# chkconfig xinetd on

Cho phép Telnet qua Firewall :

	# vi /etc/sysconfig/iptables

Thêm dòng : 
	
	-A INPUT -p tcp -m state --state NEW --dport 23 -j ACCEPT
	
Ví dụ file config iptables như sau : 

	# Firewall configuration written by system-config-firewall
	# Manual customization of this file is not recommended.
	*filter
	:INPUT ACCEPT [0:0]
	:FORWARD ACCEPT [0:0]
	:OUTPUT ACCEPT [0:0]
	-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
	-A INPUT -p icmp -j ACCEPT
	-A INPUT -i lo -j ACCEPT
	-A INPUT -p tcp -m state --state NEW --dport 23 -j ACCEPT
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
	-A INPUT -j REJECT --reject-with icmp-host-prohibited
	-A FORWARD -j REJECT --reject-with icmp-host-prohibited
	COMMIT

Sau đó restart lại `iptables`

	# service iptables restart
	
##### Phía Client 

Để telnet từ Client tới Server, trên Client ta phải cài đặt telnet cho client.

Với CentOS và các distro tương đương: 

	# yum install telnet
	
Với họ Debian như Ubuntu : 

	# sudo apt-get install telnet
	
Với Windows đã cài sẵn telnet, bạn bật dịch vụ telnet client như sau: 

`Progams and Features/ Turn Windows features on or off`

Sau đó check vào ô `Telnet Client` và OK

<img src="http://i.imgur.com/j9Quzdl.png">




<a name="wireshark"></a>
### 3. Bắt gói tin TELNET

Để truy cập vào Telnet server, ở Windows sử dụng Command Line, Linux sử dụng Terminal

	> telnet ip_server
	
Ví dụ : 

<img src="http://i.imgur.com/m5B5ep0.png">

Sau đó tiến hành đăng nhập : 

<img src="http://i.imgur.com/wgPfswS.png">

	
Sử dụng Wireshark bắt gói tin Telnet: 

<img src="http://i.imgur.com/0SIXHQI.png">

Một gói tin Telnet : 

<img src="http://i.imgur.com/crpmAKk.png">

Phân tích dòng TCP: `Analyze/Follow/TCP Stream`

<img src="http://i.imgur.com/ZWHqipm.png">

Thông tin về username, password và các lệnh thực hiện bị bắt lại dưới dạng clear text. Điều này rất nguy hiểm nếu có một người có khả năng truy cập vào router, switch, gateway ở giữa 2 máy dùng telnet.


<a name="reference"></a>
### 4. Tham khảo 

https://www.unixmen.com/installing-telnet-centosrhelscientific-linux-6-7/


[HEAD](top)