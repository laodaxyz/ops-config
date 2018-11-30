# spark部署

## 系统配置

1. 挂载数据盘
- https://help.aliyun.com/document_detail/25426.html?spm=a2c4g.11186623.2.26.660c1846cr36yn#concept_jl1_qzd_wdb
```bash
fdisk -l
硬盘分区    fdisk /dev/vdb
硬盘格式化   mkfs.ext4  -T largefile /dev/vdb1
开机自动挂载 echo /dev/vdb1 /data ext4 defaults 0 0 >> /etc/fstab
挂载         mount /dev/vdb1 /data
```

2. jdk安装 
- rpm -ivh jdk-8u181-linux-x64.rpm

3. vim /etc/hosts
```bash
172.16.80.27  spark01
172.16.80.28  spark02
172.16.80.29  spark03
```

4. ssh免登录
```bash
ssh-keygen -t rsa
ssh-copy-id -i /root/.ssh/id_rsa.pub root@172.16.80.27
ssh-copy-id -i /root/.ssh/id_rsa.pub root@172.16.80.28
ssh-copy-id -i /root/.ssh/id_rsa.pub root@172.16.80.29
```

5. 环境变量 vim /etc/profile
```bash
#java
export JAVA_HOME=/usr/java/default
export JRE_HOME=${JAVA_HOME}/jre
export PATH=${JAVA_HOME}/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib

#scala
export SCALA_HOME=/usr/local/app/scala
export PATH=$PATH:$SCALA_HOME/bin

#Hadoop
export HADOOP_HOME=/usr/local/app/hadoop
export CLASSPATH=.:$HADOOP_HOME/lib:$CLASSPATH
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_ROOT_LOGGER=INFO,console
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"

#spark enviroment
export SPARK_HOME=/usr/local/app/spark
export PATH="$SPARK_HOME/bin:$PATH"

#hive enviroment(未配置)
export HIVE_HOME=/usr/local/app/hive
export PATH=$HIVE_HOME/bin:$PATH
```

6. 上传文件
```bash
cd /usr/local/app && ll
hadoop -> hadoop-2.7.7
hadoop-2.7.7
scala -> scala-2.11.7
scala-2.11.7
spark -> spark-2.4.0-bin-hadoop2.7
spark-2.4.0-bin-hadoop2.7
```
7. 上传配置文件
- rz -f

8. hadoop初始化
- 异常处理 日志打印到屏幕 export HADOOP_ROOT_LOGGER=DEBUG,console
- namenode格式化  hadoop namenode -format
- 创建hdfs目录 hadoop fs -mkdir -p directory

9. 开机配置
```bash
#hadoop
/usr/local/app/hadoop/sbin/start-all.sh
#hadoop history
mr-jobhistory-daemon.sh --config /usr/local/app/hadoop/etc/hadoop/  start historyserver
#spark
/usr/local/app/spark/sbin/start-all.sh
#spark history
/usr/local/app/spark/sbin/start-history-server.sh
#hive(未配置)
nohup hive --service hiveserver2 &
nohup hive --service metastore &
```

10. 其他配置
- 登陆欢迎消息 /etc/motd
- ssh端口 /etc/ssh/sshd_config