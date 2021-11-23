#### top+jstack分析cpu过高原因

1. 查找出现问题的进程ID

   ```shell
   ps -ef | grep java
   ```

   

2. 查询进程下所有线程运行的情况

   ```shell
   top -Hp pid
   #shift+p 按cpu排序
   #shift+m 按内存排序
   ```

   

3. 找到cpu最高的pid，用printf "%x\n" pid转为16进制

   ```shell
   printf "%x\n" pid
   ```

   

4. 用jstack pid | grep 16进制线程ID找到线程信息

   ```shell
   jstack pid | grep -A 20 16进制线程ID
   jstack pid | grep 线程16进制ID -c 显示行数
   ```

   

