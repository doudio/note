> ### MongoDB

##### Centos下主从复制

- 安装Mongodb

  - 将压缩包复制至Linux，并解压

    - tar -zxvf mongodb-linux-x86_64-rhel62-3.4.10.tgz

  - 解压后，将文件夹名修改为mongodb，并将mongodb文件移到/usr/local目录下

    - mv mongodb-linux-x86_64-rhel62-3.4.10 mongodb
    - mv mongodb /usr/local/

  - 在/usr/local/mongodb目录下创建data/db目录及log目录，存放数据及日志

    - mkdir data
      - mkdir log
      - cd log
        - touch mongodb.log
      - mkdir db

  - 在data目录下创建mongodb启动的配置文件mongodb.conf

    - vim mongodb.conf

      ```
      #数据存放目录
      dbpath=/usr/local/mongodb/data/db
      #日志存放目录
      logpath=/usr/local/mongodb/data/log/mongodb.log
      #主服务
      master=true
      #后台运行mongodb
      fork=true
      #端口号
      port=27017
      #同步日志的大小
      oplogSize=2048
      #是否开启认证，用户登录
      #auth = true
      
      ```

  - 设置启动mongodb的环境变量

    - vim /etc/profile 

    - 在profile文件中加入：

      ```
      export PATH=/usr/local/mongodb/bin:$PATH
      ```

    - source /etc/profile 使环境变量配置生效

  - 启动主服务

    - mongod --config /usr/local/mongodb/data/mongodb.conf

- 安装从服务器，在另一台Linux中安装mongodb，在配置mongodb.conf文件时有所不同，其它步骤都一样

  ```
  #数据存放目录
  dbpath=/usr/local/mongodb/data/db
  #日志存放目录
  logpath=/usr/local/mongodb/data/log/mongodb.log
  #从服务
  slave=true
  #同步地址，主服务所在IP地址
  source=192.168.108.130:27017
  #后台运行mongodb
  fork=true
  #端口号
  port=27017
  #同步日志的大小
  oplogSize=2048  
  #是否开启认证，用户登录
  #auth = true
  ```

- 主库中修改数据，在从库中查看。

  - 从库中查看数据需要执行 rs.slaveOk() 命令：允许在从服务器上进行操作
  - 关闭防火墙 `service iptables stop` :: `service iptables start`