---
title: Centos安装caravel
date: 2016/10/25
---
#Centos安装caravel

##简介
caravel要求python2.7以上，推荐使用python3.4+的版本，所以如果是python2.6，那么请先升级python吧。

##Python2.6升级到2.7
###1.下载安装Python2.7
```
wget http://python.org/ftp/python/2.7.3/Python-2.7.3.tar.bz2
tar -jxvf Python-2.7.3.tar.bz2 
cd Python-2.7.3
./configure  make all             make install  make clean  make distclean 
```

###2.建立软链接，修改系统默认Python指向
```
mv /usr/bin/python /usr/bin/python2.6.6  ln -s /usr/local/bin/python2.7 /usr/bin/python  
```

###3.修改Yum中的Python，保证Yum正常运行
由于系统默认的Python变为2.7，Yum对此不兼容，导致Yum无法使用，这时候我们需要进行如下步骤，确保Yum的正常使用。

```
vi /usr/bin/yum
```
打开yum的运行脚本，将会看见头部处有

```
#!/usr/bin/python
```
改成

```
#!/usr/bin/python2.6.6
```
ok，试试Yum是否成功。

##安装系统依赖环境
```
sudo yum upgrade python-setuptoolssudo yum install gcc libffi-devel python-devel python-pip python-wheel openssl-devel libsasl2-devel openldap-devel
```

##安装Python环境（Python环境已齐全，可以略过此步骤）
需要Python环境：setup工具、pip工具、virtualevn组件.

###1.安装setup工具

```
wget https://bootstrap.pypa.io/ez_setup.py -O - | python
```

###2.安装pip工具

```
wget "https://pypi.python.org/packages/source/p/pip/pip-1.5.4.tar.gz#md5=834b2904f92d46aaa333267fb1c922bb" --no-check-certificate
tar -xzvf pip-1.5.4.tar.gz
cd pip-1.5.4
python setup.py install
```

###3.安装Python virtualenv
```
pip install virtualenv
```

###4.使用阿里pip源（重要）
镜像源的好处你知道，呵呵，不用你试试看。

各种系统pip配置文件路径
<pre>
1.On Unix the default configuration file is: $HOME/.config/pip/pip.conf which respects the XDG_CONFIG_HOME environment variable.
2.On Mac OS X the configuration file is $HOME/Library/Application Support/pip/pip.conf.
3.On Windows the configuration file is %APPDATA%\pip\pip.ini.
</pre>

centos在~/.pip/pip.conf文件中添加或修改

```
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```


##安装caravel

```
# Install caravel
pip install caravel

# Create an admin user
fabmanager create-admin --app caravel

# Initialize the database
caravel db upgrade

# Load some data to play with
caravel load_examples

# Create default roles and permissions
caravel init

# Start the web server on port 8088
caravel runserver -p 8088

```

##参考资源
1.【python setup工具】https://pypi.python.org/pypi/setuptools

2.【pip安装】http://www.ttlsa.com/python/how-to-install-and-use-pip-ttlsa/

3.【caravel安装】http://airbnb.io/caravel/installation.html

4.【pip阿里源】http://mirrors.aliyun.com/help/pypi

5.【pip mac配置】https://pip.pypa.io/en/stable/user_guide/#configuration

