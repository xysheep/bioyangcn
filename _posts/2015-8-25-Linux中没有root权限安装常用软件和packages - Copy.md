---
layout: post
title: Linux中没有root权限安装常用软件和packages
---
### 没有root权限安装常用软件

做大型运算需要用到服务器，一般而言我们是没有服务器的root权限的。如果每次需要安装软件都联系管理员实在效率低下，我这里总结一下没有root权限安装各种软件。大致会遇到三种情况，第一种是直接能下载到linux下的binary （比如blast、tophat等）。第二种能下载源码但是不需要额外安装依赖包（比如python和各种依赖库），最后一种是需要自己编译源码和安装相应的依赖包（比如R）。

###### 直接能下载到可执行文件
从最简单的说起，直接下载下来就是可执行的binary文件。首先在自己的home目录下建立一个新的目录叫做bin。然后运行下面代码

echo 'export PATH=$HOME/bin:$PATH' >> ~/.bash_profile
上面步骤只需要做一次，以后安装软件不需要做。然后把下载到的所有binary文件复制到~/bin目录下面去就好。重新启动terminal就可以了。

###### 能下载到源码，且不需要安装依赖包
然后说第二种情况。有源码，且不需要安装依赖包。首先在自己的home目录下建一个文件夹".local"，并把这个目录添加到.bash_profile中。这一步只需要做一次，以后安装软件不再需要做。

cd ~
mkdir .local
echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bash_profile
然后解压下载到的软件源码并进入目录。如果是tar.gz格式，用tar zxvf解压。如果是tar.bz2格式，用tar jxvf解压。如果是zip格式，就用unzip解压（有的服务器没有unzip，直接传到windows下解压）。
``` bash
tar zxvf software.tar.gz
cd software
```
接下来就是配置和安装了。
``` bash
./configure --prefix=~/.local
make
make install
```
这个时候应该就可以用了。如果是第一次安装软件，可能需要重启terminal。

能下载到源码，且需要安装依赖包

这是比较可恶的一种，很多时候依赖包还不止一两个，非常浪费时间。比如用得比较多的R，一般来说就得手动安装libpng, libreadline, libx11等等。下面以R为例。假设我的电脑上还需要安装libreadline。首先在libreadline的官网下载源码包，然后用第二类中的方法安装这个包。记得configure时把prefix设为~/.local。

然后运行以下代码安装R。

``` bash
./configure LDFLAGS="-L$HOME/.local/lib" CPPFLAGS="-I$HOME/.local/include" --prefix=~/.local
make
make install
```
这里在configure一步指定了两个环境变量，LDFLAGS和CPPFLAGS。意思是到这些位置去找依赖库。这里面需要说明的是，在64位的linux系统中，这一步不能通过修改.bash_profile实现。如果谁有更好的方法欢迎交流。

安装python和R的packages

如果按照上面的步骤安装R，那么R的packages可以直接用install.packages()正常安装不需要多说。python略有不同。

下载到的python源码会包含一个setup.py文件。直接使用
``` bash
python setup.py install
```
就是安装。但是这样安装需要root权限。加一个参数即可指定安装到本地。
``` bash
python setup.py install --user
```

最后，再回到R的packages上。虽然前面说R的packages可以直接安装，但是可能到如下问题。安装R package “curl”和“xml”时，会报错提示需要依赖库libcurl和libxml2。这两个库不需要在编译R的时候安装，但是要用curl和xml两个R package的时候就需要用到。这两个package的安装跟前面“能下载到源码，且不需要安装依赖包”中的内容类似。不同的地方是安装libxml2需要额外设置环境变量指向python的库。运行以下语句


echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/.local/lib:$HOME/.local/lib/python2.7' >> ~/.bash_profile
echo 'export C_INCLUDE_PATH=$C_INCLUDE_PATH:$HOME/.local/include:$HOME/.local/include/python2.7' >> ~/.bash_profile
以上语句运行完毕后，libcurl和libxml2都可以被R直接调用。安装xml和curl的包到此成功