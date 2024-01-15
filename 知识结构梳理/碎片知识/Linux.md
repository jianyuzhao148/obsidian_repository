- Linux 查看端口占用：netstat -tnlp
- 设置终端代理：export http_proxy=http://proxyAddress:port
- 在终端中打开文件管理器（UI）：nautilus [path]
- 用域名向DNS服务器查询IP：host -t A 域名
- 解压tar.gz文件：tar -xzvf *.tar.gz
- 非root用户 不可使用1024以下端口(不能直接使用80端口)
- MySQL
    - 使用mysql_config 查看libs_r表示需要在g++命令行添加的参数
    - CREATE USER 'test'@'%' IDENTIFIED BY '123456';//创建用户并设置密码
    - GRANT ALL PRIVILEGES ON _._ TO 'test'@'%';//表示将所有db开放给test用户
    - Flush privileges;//刷新权限表
- Docker
    - docker执行mysql镜像，映射本机3306端口，保存数据到本机/home/mysql/datadir，设置密码为123456：docker run -p 3306:3306 -v /home/mysql/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
    - docker执行redis镜像，映射本机6379端口，保存数据到本机/home/redis/data，以/usr/local/etc/redis/redis.conf配置启动：docker run -itd -p 6379:6379 --name redis -v /home/redis/redis.conf:/usr/local/etc/redis/redis.conf -v /home/redis/data:/data redis redis-server /usr/local/etc/redis/redis.conf
    - docker执行nginx镜像，映射80端口：docker run --name=nginx -d -p 80:80 nginx
    - docker执行Apache镜像，映射8080端口，映射cgi-bin目录，映射httpd.conf配置文件：docker run -p 8080:80 -v /var/www/cgi-bin:/usr/local/apache2/cgi-bin -v /var/www/cgi-bin/httpd.conf:/usr/local/apache2/conf/httpd.conf -d httpd
    - 开机启动docker：systemctl enable docker
    - 启动docker：systemctl start docker
    - 停止docker：systemctl restart docker
    - 查看镜像log：docker logs 镜像ID
    - 打开指定镜像的命令行交互：docker exec -it 容器ID /bin/bash
    - 从docker中复制文件到本机：docker cp 镜像ID:/etc/nginx/conf.d/default.conf ./
    - 非root用户无法使用docker：
        1. sudo groupadd docker #添加docker用户组
        2. sudo gpasswd -a $USER docker #将登陆用户加入到docker用户组中
        3. newgrp docker #更新用户组