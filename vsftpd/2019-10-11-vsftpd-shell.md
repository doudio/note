* demo

```shell
# 生成 下载脚本
./synftp.sh get
# 生成 上传脚本
./synftp.sh get
```

* synftp.sh
* 修改配置

```shell
#!/bin/sh

# check param
if [ $# -ne 1 ] ; then
echo "parameters error"
exit
else
if [ $1 != "get" ] && [ $1 != "put" ] ; then
echo "no param $1"
exit
fi
fi

# set variables
ftp_host="255.255.42.99"
ftp_port="21"
ftp_user="user"
ftp_password="pas"
folder_local="/data/tmp"
folder_remote="/"
temp_shell="sync_temp.sh"

# login
cat > $temp_shell << EOF
ftp -v -n << !
open $ftp_host $ftp_port
user $ftp_user $ftp_password
lcd $folder_local
cd $folder_remote
bin
prompt off
EOF

if [ $1 =  "get" ]; then
echo "add mget * into $temp_shell"
echo "mget *" >> $temp_shell
elif [ $1 = "put" ]; then
for i in `ls $folder_local`; do
echo "add put $i into $temp_shell"
echo "put $i" >> $temp_shell
done
fi

# Exit FTP
cat >> $temp_shell << EOF
quit
!
EOF

# execute shell
#chmod 777 $temp_shell
#echo "execute $temp_shell"
#./$temp_shell
#rm $temp_shell
```