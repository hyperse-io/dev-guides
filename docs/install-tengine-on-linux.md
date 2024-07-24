# ECS install tengine guides

## Install the necessary compilation environment well

Before installing, first check to see if you have installed some of the lib libraries that nginx modules rely on, such as g++, gcc, pcre-devel, openssl-devel, and zlib-devel. Therefore, it is best to run the following commands one by one, the installed ones will prompt not to install, and the ones that are not installed or need to be updated will install and update:

```shell
yum install gcc-c++
yum -y install pcre pcre-devel
yum -y install zlib zlib-devel
yum -y install openssl openssl-devel
```

## Download `tengine-2.3.3` and `headers-more-nginx-module-0.33` to the directory `/opt/softwares` and decompress the software in the current directory via (`tar zvxf xxxx`)

## Configure

```shell
cd /opt/softwares/tengine-2.3.3
./configure --prefix=/opt/tengine  --add-module=/opt/softwares/headers-more-nginx-module-0.33
make && make install
```

## Configure Tengine and set tengine to automatically start upon startup

```shell
# Boot directory
/usr/lib/systemd/system
```

Set the system to boot the directory, so I put the files in the system directory.

```shell
vim /lib/systemd/system/nginx.service
```

```shell
[Unit]
Description=The nginx HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/opt/tengine/logs/nginx.pid
ExecStartPre=/opt/tengine/sbin/nginx -t
ExecStart=/opt/tengine/sbin/nginx -c /opt/tengine/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

Modifying file permissions

```shell
chmod 745 nginx.service
```

Enable service

```shell
systemctl enable nginx.service
```

Disable service

```shell
systemctl disable nginx.service
```

View the current status of the service

```shell
systemctl status nginx.service
```

Stop the nginx service

```shell
systemctl stop nginx.service
```

Start the nginx service

```shell
systemctl start nginx.service
```

Restart the service nginx service

```shell
systemctl restart nginx.service
```

View all started services

```shell
systemctl list-units --type=service
```
