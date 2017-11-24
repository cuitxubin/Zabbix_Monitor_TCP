#*ZABBIX 3.0 监控服务器TCP连接状态*


##监控原理
	1、TCP连接可以使用命令获取：
	*# netstat -an|awk '/^tcp/{++S[$NF]}END{for(a in S) print a,S[a]}'

	2、可以使用man netstat查看TCP的各种状态信息描述：
	LISTEN - 侦听来自远方TCP端口的连接请求； 
	SYN-SENT -在发送连接请求后等待匹配的连接请求； 
	SYN-RECEIVED - 在收到和发送一个连接请求后等待对连接请求的确认； 
	ESTABLISHED- 代表一个打开的连接，数据可以传送给用户； 
	FIN-WAIT-1 - 等待远程TCP的连接中断请求，或先前的连接中断请求的确认；
	FIN-WAIT-2 - 从远程TCP等待连接中断请求； 
	CLOSE-WAIT - 等待从本地用户发来的连接中断请求； 
	CLOSING -等待远程TCP对连接中断的确认； 
	LAST-ACK - 等待原来发向远程TCP的连接中断请求的确认； 
	TIME-WAIT -等待足够的时间以确保远程TCP接收到连接中断请求的确认； 
	CLOSED - 没有任何连接状态；

##使用方法
    1、将status_TCP.conf文件放置到/etc/zabbix/zabbix_agentd.d目录。

   	2、将脚本tcp_status.sh放置到目录/etc/zabbix/scripts下，如果目录不存在，则创建目录；赋予脚本执行权限及添加执行权限和tcp_status的属主和属组。
		*# mkdir /etc/zabbix/scripts
		*# chmod +x /etc/zabbix/scripts/tcp_status.sh
		*# chown zabbix:zabbix /etc/zabbix/scripts/tcp_status.sh

    3、因为脚本是把tcp的一些信息存放在/tmp/下，为了zabbix可以读取到我们设置zabbix可以读的权限：
		*# touch /tmp/tcp_status.txt
		*# chown zabbix:zabbix /tmp/tcp_status.txt

	4、重启zabbix agent 
		*# service zabbix-agent restart

    5、在zabbix server中导入模板(zabbix版本>=3.0,版本低于3.0可能无法导入):Template_TCP_Status_templates.xml

	6、检验KEY：
		*# zabbix_get -s 127.0.0.1  -k tcp.status[established]
			270
		*# zabbix_get -s 127.0.0.1  -k tcp.status[lastack]
			0
		*# zabbix_get -s 127.0.0.1  -k tcp.status[listen]
			11

		可查看server端日志：
		*# tailf /var/log/zabbix/zabbix_server.log

		 	21178:20171124:174831.855 item "Zabbix server:tcp.status[closed]" became supported
 			21185:20171124:174832.855 item "Zabbix server:tcp.status[closewait]" became supported
 			21178:20171124:174833.860 item "Zabbix server:tcp.status[closing]" became supported
 			21185:20171124:174834.861 item "Zabbix server:tcp.status[established]" became supported
			21178:20171124:174835.865 item "Zabbix server:tcp.status[finwait1]" became supported
 			21185:20171124:174836.866 item "Zabbix server:tcp.status[finwait2]" became supported
 			21178:20171124:174837.871 item "Zabbix server:tcp.status[lastack]" became supported
 			21185:20171124:174838.873 item "Zabbix server:tcp.status[listen]" became supported
 			21178:20171124:174839.877 item "Zabbix server:tcp.status[synrecv]" became supported
 			21185:20171124:174840.878 item "Zabbix server:tcp.status[synsent]" became supported
 			21178:20171124:174841.882 item "Zabbix server:tcp.status[timewait]" became supported




###参考文档
	1、http://www.xuliangwei.com/xubusi/637.html
	2、http://blog.csdn.net/reblue520/article/details/52274354
	3、https://www.abcdocker.com/abcdocker/2652
