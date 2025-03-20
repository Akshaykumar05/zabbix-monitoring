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
------------------
### Zabbix Installation
Setup Zabbix master on one VM (using docker-compose) and Zabbix-agent on the other VM (Node-1)
#### Steps:
1. Create a folder of Zabbix in opt
   ```
   mkdir zabbix
   ```
2. Create and update docker-compose.yml
   ```
   vim docker-compose.yml
   ```
   Update the yml file with the following detail

```
version: '3.3'

services:
  mysql-server:
    image: mysql:8.0-oracle
    container_name: mysql-server
    environment:
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix"
      MYSQL_ROOT_PASSWORD: "root"
    volumes:
      - ./mysql:/var/lib/mysql:Z  # Using the same folder as the compose file
    networks:
      - default
    restart: unless-stopped
    command: --character-set-server=utf8 --collation-server=utf8_bin --default-authentication-plugin=mysql_native_password

  zabbix-java-gateway:
    image: zabbix/zabbix-java-gateway:alpine-6.4-latest
    container_name: zabbix-java-gateway
    networks:
      - default
    restart: unless-stopped

  zabbix-server-mysql:
    image: zabbix/zabbix-server-mysql:alpine-6.4-latest
    container_name: zabbix-server-mysql
    environment:
      DB_SERVER_HOST: "mysql-server"
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix"
      MYSQL_ROOT_PASSWORD: "root"
    networks:
      - default
    ports:
      - "10051:10051"
    restart: unless-stopped

  zabbix-web-nginx-mysql:
    image: zabbix/zabbix-web-nginx-mysql:alpine-6.4-latest
    container_name: zabbix-web-nginx-mysql
    environment:
      ZBX_SERVER_HOST: "zabbix-server-mysql"
      DB_SERVER_HOST: "mysql-server"
      MYSQL_DATABASE: "zabbix"
      MYSQL_USER: "zabbix"
      MYSQL_PASSWORD: "zabbix"
      MYSQL_ROOT_PASSWORD: "root"
    networks:
      - default
    ports:
      - "80:8080"
    restart: unless-stopped
```
3. Now start the containers using command:
   ```
   docker-compose up -d
   ```
   If everything goes well, we can open the url using localhost (user- admin, password- zabbix), it should be as

   ![Screenshot 2025-03-14 233225](https://github.com/user-attachments/assets/af6719c5-d608-42d1-9f51-7d9674710099)

 4. Now set up Zabbix-agent on the other VM (Node-1)
    ### Become root user
    ### Start new shell session with root privileges.
    ```
    sudo -s
    ```

    ### Install Zabbix repository

    ```
    wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_6.0+ubuntu20.04_all.deb
    dpkg -i zabbix-release_latest_6.0+ubuntu20.04_all.deb
    apt update
    ```
    ### Install Zabbix agent
    ```
    apt install zabbix-agent
    ```

    ###  Start Zabbix agent process
    Start Zabbix agent process and make it start at system boot.

    ```
    systemctl restart zabbix-agent
    systemctl enable zabbix-agent
    ```
    
    ### Add the host on Zabbix Master url on dashboard
    1. Create a new host
    2. Use the Agent IP and other following details as shown in snapshot.
       <img width="688" alt="zabbix-host-add" src="https://github.com/user-attachments/assets/ef0bf2c6-ef6c-42cb-9692-f6257d04770e" />


