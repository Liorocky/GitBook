# tar命令去除目录结构

在打包时如果源文件包含绝对路径，打包的文件重新解压也会包含有目录信息，所以如果不需要目录信息需要自己在打包的地方配置一下。

tar -zcvf /opt/test/a.tar.gz -C/opt/test/ a.log \(注意：-C与文件夹之间没有空格，文件夹与文件中间有空格\)

命令结构为：打包命令+空格+压缩后文件存放目录及名称+空格+"-C"+源文件目录+空格+文件名称

![img](https://img-blog.csdnimg.cn/20191125174315777.png)

使用时命令需要一一对应。

使用tar命令只打包：

命 令 作 用 tar -cvf 123.tar 123 打包单个文件 tar -cvf 123.tar 1.txt 123 打包多个文件 tar -xvf 123.tar 解包 tar -tf 123.tar 查看打包文件列表 tar -cvf 123.tar --exclude 1.txt --exclude 23 打包时排除不需要打包的文件或文件夹（排除文件夹是最后不能有‘/’） 使用tar命令并压缩：

命 令 作 用 tar -zcvf 123.tar.gz 123 打包并压缩成gzip压缩包 tar -zxvf 123.tar.gz 解包并解压缩gzip压缩包 tar -jcvf 123.bz2 123 打包并压缩成bzip2压缩包 tar -jxvf 123.bz2 解包并解压缩bzip2压缩包 tar -Jcvf 123.xz 123 打包并压缩成xz压缩包 tar -Jxvf 123.xz 解包并解压缩xz压缩包 tar -tf 123.bz2/123.gz/123.xz 查看压缩包文件列表 命令部分原文地址：[http://www.mamicode.com/info-detail-1870751.html](http://www.mamicode.com/info-detail-1870751.html)

