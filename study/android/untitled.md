# install

#### 配置阿里云镜像

> [https://www.jianshu.com/p/b038bd95444b](https://www.jianshu.com/p/b038bd95444b)

如果你之前设置过 Android Studio 的 HTTP Proxy，然后又取消了代理设置，那么很有可能 Andoid Studio gradle 再次编译时仍然会走代理设置，造成依赖资源一直下载失败。 分为两种：

* 单独工程配置镜像
* 整体工程配置

## 单独工程配置

### 1.删除 Android Studio 的代理设置

首先你需要确认你已经在 Settings -&gt; Appearance&Behavior -&gt; System Settings -&gt; HTTP Proxy 中选中了 No Proxy。

然后找到项目根目录下的 gradle.properties 文件，打开查看该文件中是否有关于 proxy 设置（代理的地址和端口）的相关语句，删除这些内容，让该文件看起来大致是这个样子（如果你没有添加其他设置的话）：

最后，你需要找到你的另一个 gradle.properties 文件：C:\Users\Administrator.gradle\gradle.properties：

打开该文件，和上面的操作一样，删除与 proxy 设置相关的语句，让该文件的内容看起来大致是这样（如果你没有添加其他设置的话）：

#### 2.让项目通过阿里云 maven jcenter 下载依赖资源

打开项目根目录下的 build.gradle（Project:项目名称一级的gradle），如下所示添加阿里 maven 库地址：

```go
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
    repositories {
        maven {
          url 'https://maven.aliyun.com/repository/public/'
        }
        mavenLocal()
        google()
        mavenCentral()
    }
    dependencies {
        classpath "com.android.tools.build:gradle:7.0.1"
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.20"

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

重新 SYNC 项目，编译时的资源下载一般就会如丝般顺滑了。

## 全局配置

**在C:\Users\Administrator.gradle中新建init.gradle，并输入如下内容：**

> ## 使用 allowInsecureProtocol 属性解决 gradle 的仓库地址不安全警告

```ruby
allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN_JCENTER_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
            }
        }
        maven {
            allowInsecureProtocol = true
            url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
        }
    }
}
```

重启Android Studio 并clean rebuild 即可

> Build was configured to prefer settings repositories over project repositories but repository 'maven' was added by build file 'build.gradle'

![image-20210826234324397](http://lior-image.oss-cn-beijing.aliyuncs.com/img/image-20210826234324397.png)

> [http://blog.linrty.com/2021/05/09/%E5%AE%89%E5%8D%93%E5%BC%95%E5%85%A5jitpack.io%E6%97%B6%E5%8F%91%E7%94%9F%E7%9A%84%E8%AF%A1%E5%BC%82%E4%BA%8B%E4%BB%B6%E5%8F%8A%E8%A7%A3%E5%86%B3/](http://blog.linrty.com/2021/05/09/%E5%AE%89%E5%8D%93%E5%BC%95%E5%85%A5jitpack.io%E6%97%B6%E5%8F%91%E7%94%9F%E7%9A%84%E8%AF%A1%E5%BC%82%E4%BA%8B%E4%BB%B6%E5%8F%8A%E8%A7%A3%E5%86%B3/)

### 异常

#### 解决 Android Studio 报SDK tools directory is missing

[Nothing to do! Android SDK is up to date. SDK emulator directory is missing](https://stackoverflow.com/questions/67527941/nothing-to-do-android-sdk-is-up-to-date-sdk-emulator-directory-is-missing)

[https://www.cnblogs.com/liminghuang/p/8976258.html](https://www.cnblogs.com/liminghuang/p/8976258.html)

Running Android Emulator Hypervisor Driver for AMD Processors installer

[https://androidstudio.googleblog.com/2019/10/android-emulator-hypervisor-driver-for.html](https://androidstudio.googleblog.com/2019/10/android-emulator-hypervisor-driver-for.html)

![image-20210826225603616](http://lior-image.oss-cn-beijing.aliyuncs.com/img/image-20210826225603616.png)

![image-20210826225729247](http://lior-image.oss-cn-beijing.aliyuncs.com/img/image-20210826225729247.png)

