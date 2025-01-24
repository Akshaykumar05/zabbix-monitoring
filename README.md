# zabbix-monitoring
Here we'll see how to add a website in Zabbix for monitoring. We have already working Zabbix with other running websites.

First we login into server using the IP and username
```
ssh etrans-infra-mon10@10.242.36.130
```
```
sudo bash
```
```
cat /etc/os-release
```
```
cd /tmp/
```
```
ls
```
```
dnf install zabbix-agent
```
```
cd /etc/zabbix/
```
```
ls
```
```
systemctl start zabbix-agent
```
```
systemctl status zabbix-agent
```
```
vim zabbix_agentd.conf
```
```
ip a
```
```
systemctl restart zabbix-agent
```
```
systemctl status zabbix-agent
```

