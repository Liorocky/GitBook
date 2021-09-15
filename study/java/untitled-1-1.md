# SpringBoot异常

最近新搭的一个项目，启动时，会报MalformedInputException这个异常，

百度了很久，网上说的千篇一律，

有的说，把yml复制到txt再复制回来

有的说，设置eclipse的utf-8环境

这些根本没有根治这个问题。

出现这个的原因，就是解析yml文件时，中文字符集不是utf-8的原因，

但是通过cmd命令，mvn clean compile 后，项目又可以成功运行

找了很久问题，使用eclipse和idea同时测试，最终发现，是maven在项目编译时，默认字符集编码是GBK

所以我们只需要在pom文件中设置，编译时编码utf-8即可

代码如下：

 UTF-8&lt;/project.build.sourceEncoding&gt;  
UTF-8&lt;/project.reporting.outputEncoding&gt;  
UTF-8&lt;/maven.compiler.encoding&gt; 总结：

这个问题，在一般成熟的项目里面是不会出现的，他一般出现在新建的项目上面，因为一般新建的项目有些配置不够完善，这一块是需要注意的地方。

