### 安装redis（Linux）

1. 安装gcc

   ~~~
   yum install gcc
   
   解释：redis是用c语言编写，需要先使用gcc编译redis
   ~~~

2. 上传 解压redis安装包

   ~~~
   tar -zxvf redis-3.0.7.tar.gz
   ~~~

3. 编译

   ~~~
   命令：make
   ~~~

   ![1571385896450](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yx5wmbqj30q507ntbo.jpg)

   注意：在redis的目录中执行make命令

   安装成功的标志

   ![1571385952629](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yx6qocej30q207zn23.jpg)

4. 启动redis服务端

   ~~~
   进入redis安装目录的src目录
   
   使用./redis-server ../redis.conf 启动redis数据库
   ~~~

   ![1571386111323](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yx710w9j30q10aatah.jpg)

   

5. 启动redis命令行客户端

   ~~~
   新Linux操作窗口中 执行 ./redis-cli
   
   该窗口就是redis的命令行窗口 可以执行redis中的命令
   ~~~

   ![1571386200275](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yx7melnj30q506ljry.jpg)

6. 简单的测试redis存取命令

   ![1571386436636](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yx80wmhj30mu05c0tm.jpg)