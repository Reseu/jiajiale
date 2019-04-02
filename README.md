# Python-Django-稼稼乐项目

该项目包含了实际开发中的电商项目中大部分的功能开发和知识点实践。

功能:用户注册，用户登录，购物车，用户中心，首页，订单系统，地址信息管理，商品列表，商品详情，支付功能等等，是一个完整的电商项目流程

## 技术栈
python + django + mysql + redis + celery + FastDFS(分布式图片服务器) + nginx


## 目标功能:
- [ ] 功能模块
    - [x] 用户模块
        - [x] 注册
        - [x] 登录
        - [x] 激活(celery)
        - [x] 退出
        - [x] 个人中心
        - [x] 地址管理
    - [x] 商品模块
        - [x] 首页(celery)
        - [x] 商品详情
        - [x] 商品列表
        - [x] 搜索功能(haystack+whoose)
    - [x] 购物车模块(redis)
        - [x] 增加
        - [x] 删除
        - [x] 修改
        - [x] 查询
    - [x] 订单模块
        - [x] 确认订单页面
        - [x] 订单创建
        - [x] 请求支付(支付宝)
        - [x] 查询支付结果
        - [x] 评论

## 运行环境

[配置环境文件]   setting.py

[虚拟环境安装和启动方式]

```
安装同一个包的不同版本，后安装的包会把原来安装的包覆盖掉。这样，如同一台机器上两个项目依赖于相同包的不同版本，则会导致一些项目运行失败。 解决的方案就是：虚拟环境*

虚拟环境是真实python环境的复制版本。

安装虚拟环境的命令
安装虚拟环境
sudo pip install virtualenv

安装虚拟环境扩展包
sudo pip install virtualenvwrapper

编辑家目录下面的.bashrc文件,添加下面两行

export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
使用 source .bashrc 使其生效

安装中可能遇到的错误
/usr/bin/python: No module named virtualenvwrapper
错误原因：Ubuntu安装了2.7和3.x两个版本的python,在安装时使用的是sudo pip3 install virtualenvwrapper 在运行的时候默认使用的是python2.x,但在python2.x中不存在对应的模块
解决方法：在.bashrc文件里面加入如下命令即可
VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
----

VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
virtualenv操作命令
创建虚拟环境命令:
    mkvirtualenv 虚拟环境名

创建Python3虚拟环境:
    mkvirtualenv -p python3 虚拟环境名
   
进入虚拟环境工作:
    workon 虚拟环境名
    
查看机器上有多少个虚拟环境：
    workon
    
退出虚拟环境：
    deactivate
    
删除虚拟环境：
    rmvirtualenv 虚拟环境名
```

[celery分布式任务队列]

```
任务队列是一种跨线程、跨机器工作的一种机制.

任务队列中包含称作任务的工作单元。有专门的工作进程持续不断的监视任务队列，并从中获得新的任务并处理.

celery通过消息进行通信，通常使用一个叫Broker(中间人)来协client(任务的发出者)和worker(任务的处理者). clients发出消息到队列中，broker将队列中的信息派发给worker来处理。

一个celery系统可以包含很多的worker和broker，可增强横向扩展性和高可用性能。
celery

celery
安装：pip install -U Celery
启动：celery -A celery_tasks.tasks worker -l info
Broker
Celery需要一种解决消息的发送和接受的方式，我们把这种用来存储消息的的中间装置叫做message broker, 也可叫做消息中间人。 作为中间人，我们有几种方案可选择：

RabbitMQ
RabbitMQ是一个功能完备，稳定的并且易于安装的broker. 它是生产环境中最优的选择。使用RabbitMQ的细节参照以下链接： http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html#broker-rabbitmq

如果我们使用的是Ubuntu或者Debian发行版的Linux，可以直接通过下面的命令安装RabbitMQ: sudo apt-get install rabbitmq-server 安装完毕之后，RabbitMQ-server服务器就已经在后台运行。如果您用的并不是Ubuntu或Debian, 可以在以下网址： http://www.rabbitmq.com/download.html 去查找自己所需要的版本软件。

Redis
Redis也是一款功能完备的broker可选项，但是其更可能因意外中断或者电源故障导致数据丢失的情况。 关于是有那个Redis作为Broker，可访下面网址： http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#broker-redis

当前项目采用redis为Broker
定义celery任务需要使用@app.task装饰器
当前项目使用celery启动的任务为:
发送邮件
生成首页静态文件
celery可以分布式操作,放在别的电脑上启动,在当前项目中,我并没有使用另外的虚拟电脑(虚拟机),所以设置的redis地址为:app = Celery('celery_tasks.tasks', broker='redis://127.0.0.1:6379/8')为当前虚拟机的redis
由于celery需要先开启中间人来操作任务,所以将整个项目文件复制一份放到别外的虚拟环境中启动
任务需要Django环境,所以需要启动Django环境
```

[redis环境安装]

```
redis下载
当前下载的版本为稳定版3.2版本
sudo wget http://download.redis.io/releases/redis-3.2.8.tar.gz
解压
tar -zxvf redis-3.2.8.tar.gz
放到/usr/local目录下
sudo mv ./redissudo mv ./redis
进入redis目录
cd /usr/local/redis/
生成
sudo make
测试,这段运行时间会较长
sudo make intall
安装,将redis的命令安装到/usr/local/bin/⽬录
sudo make install
安装完成后，我们进入目录/usr/local/bin中查看
cd /usr/local/bin
ls -all
image
redis-server redis服务器
redis-cli redis命令行客户端
redis-benchmark redis性能测试工具
redis-check-aof AOF文件修复工具
redis-check-rdb RDB文件检索工具

配置⽂件，移动到/etc/⽬录下
配置⽂件⽬录为/usr/local/redis/redis.conf
sudo cp /usr/local/redis/redis.conf /etc/redis/

下载过程中可能出现的错误
安装redis时遇到You need tcl 8.5 or newer in order to run the Redis test
下载安装tcl：
1. sudo wget http://downloads.sourceforge.net/tcl/tcl8.6.8-src.tar.gz
2. sudo tar xzvf tcl8.6.8-src.tar.gz （解压到usr/local/下的)
3. cd  /usr/local/tcl8.6.8/unix/
4. sudo ./configure
5. sudo make
6. sudo make install
- 由于tcl下载的服务器在外国,可能下载不了,可以使用下载好的包tcl8.6.1-src.tar.gz,然后继续执行2-6步
redis配置
redis配置信息在/etc/redis/redis.conf下
sudo vi /etc/redis/redis.conf

绑定ip：如果需要远程访问，可将此⾏注释，或绑定⼀个真实ip
bind 127.0.0.1

端⼝，默认为6379
port 6379

是否以守护进程运⾏
- 如果以守护进程运⾏，则不会在命令⾏阻塞，类似于服务 如果以⾮守护进程运⾏，则当前终端被阻塞 设置为yes表示守护进程，设置为no表示⾮守护进程 推荐设置为yes
daemonize yes

数据⽂件
dbfilename dump.rdb

数据⽂件存储路径
dir /var/lib/redis

⽇志⽂件
logfile /var/log/redis/redis-server.log

数据库，默认有16个
database 16

主从复制，类似于双机备份
slaveof

当前项目redis配置文件

redis操作命令
服务器端
服务器端的命令为
redis-server

可以使⽤help查看帮助⽂档
redis-server --help

推荐使⽤服务的⽅式管理redis服务
启动: sudo service redis start
停⽌:sudo service redis stop
重启 sudo service redis restart

其他
ps -ef|grep redis 查看redis服务器进程
sudo kill -9 pid 杀死redis服务器
sudo redis-server /etc/redis/redis.conf 指定加载的配置文件

客户端
客户端的命令为redis-cli

可以使⽤help查看帮助⽂档
redis-cli --help

连接redis
redis-cli

切换数据库
数据库没有名称，默认有16个，通过0-15来标识，连接redis默认选择第一个数据库
select n            
```

[FastDFS安装和配置]

```
安装fastDFS依赖包
解压缩libfastcommon-master.zip
进入到libfastcommon-master的目录中
执行 sudo ./make.sh
执行 sudo ./make.sh install
安装fastDFS
解压缩fastdfs-master.zip
进入到 fastdfs-master目录中
执行 ./make.sh
执行 sudo ./make.sh install
配置跟踪服务器tracker tracker conf文件
sudo cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
在/home/python/目录中创建目录 fastdfs/tracker
mkdir –p /home/python/fastdfs/tracker
编辑/etc/fdfs/tracker.conf配置文件
sudo vim /etc/fdfs/tracker.conf 修改 base_path=/home/python/fastdfs/tracker
配置存储服务器storage storage conf文件
sudo cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
在/home/python/fastdfs/ 目录中创建目录 storage
mkdir –p /home/python/fastdfs/storage
编辑/etc/fdfs/storage.conf配置文件 sudo vim /etc/fdfs/storage.conf 修改内容： base_path=/home/python/fastdfs/storage
store_path0=/home/python/fastdfs/storage
tracker_server=自己ubuntu虚拟机的ip地址:22122
启动tracker和storage
Trackerd服务
sudo /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
storge服务
sudo /usr/bin/fdfs_storaged /etc/fdfs/storage.conf start

测试是否安装成功 client.conf文件
sudo cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf
编辑/etc/fdfs/client.conf配置文件
sudo vim /etc/fdfs/client.conf
修改内容：
base_path=/home/python/fastdfs/tracker
tracker_server=自己ubuntu虚拟机的ip地址:22122
上传文件测试：
fdfs_upload_file /etc/fdfs/client.conf 要上传的图片文件
如果返回类似group1/M00/00/00/rBIK6VcaP0aARXXvAAHrUgHEviQ394.jpg的文件id则说明文件上传成功
```

[Nginx及fastdfs-nginx-module安装]

```
nginx和fastDFS-nginx-module安装
解压缩 nginx-1.8.1.tar.gz
解压缩 fastdfs-nginx-module-master.zip
进入nginx-1.8.1目录中
执行
sudo ./configure --prefix=/usr/local/nginx/ --add-module=fastdfs-nginx-module-master解压后的目录的绝对路径/src 如:
sudo ./configure --prefix=/usr/local/nginx/ --add-module=/home/hyw/下载/fastdfs-nginx-module-master/src
sudo make
sudo make install
sudo cp fastdfs-nginx-module-master解压后的目录中src下的mod_fastdfs.conf /etc/fdfs/mod_fastdfs.conf
修改配置文件：mod_fastdfs.conf文件
sudo vim /etc/fdfs/mod_fastdfs.conf
修改内容：
connect_timeout=10
tracker_server=自己ubuntu虚拟机的ip地址:22122
url_have_group_name=true
store_path0=/home/python/fastdfs/storage
sudo cp 解压缩的fastdfs-master/conf/http.conf /etc/fdfs/http.conf
sudo cp 解压缩的fastdfs-master/conf/mime.types /etc/fdfs/mime.types
sudo vim /usr/local/nginx/conf/nginx.conf
在http部分中添加配置信息如下：nginx.conf文件
server {
    listen       8888;
    server_name  localhost;
    location ~/group[0-9]/ {
        ngx_fastdfs_module;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
    root   html;
    }
}
启动nginx
sudo /usr/local/nginx/sbin/nginx
可能出现的错误
安装nginx时遇到的包问题 https://blog.csdn.net/z920954494/article/details/52132125
Nginx编译时报错 https://blog.csdn.net/u010889616/article/details/82867091
```

[全文检索引擎-生成jieba分词引擎步骤]

```
全文检索不同于特定字段的模糊查询，使用全文检索的效率更高，并且能够对于中文进行分词处理
haystack：全文检索的框架，支持whoosh、solr、Xapian、Elasticsearc四种全文检索引擎，点击查看官方网站
whoosh：纯Python编写的全文搜索引擎，虽然性能比不上sphinx、xapian、Elasticsearc等，但是无二进制包，程序不会莫名其妙的崩溃，对于小型的站点，whoosh已经足够使用，点击查看whoosh文档
jieba：一款免费的中文分词包，如果觉得不好用可以使用一些收费产品
分词
如： 很不错的草莓
分解成：很/不错/的/草莓
安装和配置
虚拟环境中安装需要的包
pip install django-haystack
pip install whoosh
pip install jieba
修改settings.py文件，安装应用haystack
INSTALLED_APPS = (
    ...
    'haystack',
)
在settings.py文件中配置搜索引擎
...
HAYSTACK_CONNECTIONS = {
    'default': {
        #使用whoosh引擎--jieba中心分词包
        'ENGINE': 'haystack.backends.whoosh_cn_backend.WhooshEngine',
        #索引文件路径
        'PATH': os.path.join(BASE_DIR, 'whoosh_index'),
    }
}
#当添加、修改、删除数据时，自动生成索引
HAYSTACK_SIGNAL_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor'
创建引擎及索引
在那个应用中使用到索引就在目录下创建search_indexes.py文件(固定写法)
apps/goods目录结构：
------------------------------
|-- __init__.py
|-- __pycache__
|-- admin.py
|-- migrations
|   |-- 0001_initial.py
|   |-- __init__.py
|   `-- __pycache__
|       |-- 0001_initial.cpython-36.pyc
|       `-- __init__.cpython-36.pyc
|-- models.py
|-- search_indexes.py    # this
|-- tests.py
|-- urls.py
`-- views.py
# search_indexes.py
# --------------------------
# 定义索引类
from haystack import indexes
# 导入模型类
from goods.models import GoodsSKU

#指定对于某个类的某些数据建立索引
class GoodsSKUIndex(indexes.SearchIndex, indexes.Indexable):
# 索引字段
# use_template=True指定根据表中的那些字段建立索引文件，把说明放在一个文件中
text = indexes.CharField(document=True, use_template=True)

def get_model(self):
    # 返回你的模型类
    return GoodsSKU

# 建立索引的数据
def index_queryset(self, using=None):
    return self.get_model().objects.all()
在django项目中templates目录下创建search/indexes/goods/目录(固定写法)
# search/indexes/目录添加你用到的应用的文件夹,命名要和应用名字一样
# 如用到了goods,那么就在目录中添加goods目录
# goods目录中的文件名字也是固定的： 
# 应用名字 + info_text.txt = goodsinfo_text.txt 

# templates/search/indexes目录结构:
|-- search
|   |-- indexes
|   |   `-- goods
|   |       `-- goodssku_text.txt
|   |-- search.html
|   `-- search1.html
目录中创建goodsinfo_text.txt
# 指定数据表中的那些字段建立索引数据
{{ object.name }} # 根据商品的名称建立索引
{{ object.desc }} # 根据商品的简介建立索引
{{ object.goods.detail }} # 根据商品的详情建立索引
使用jieba分词设置
找项目虚拟环境中的haystack目录
cd
cd .virtualenv/虚拟环境名字/lib/python3.6/site-packages/haystack
如： /home/hyw/.virtualenvs/bj18_py3/lib/python3.6/site-packages/haystack
在上面的目录中创建ChineseAnalyzer.py文件,写入下面的代码
import jieba
from whoosh.analysis import Tokenizer, Token

class ChineseTokenizer(Tokenizer):
    def __call__(self, value, positions=False, chars=False,
                 keeporiginal=False, removestops=True,
                 start_pos=0, start_char=0, mode='', **kwargs):
        t = Token(positions, chars, removestops=removestops, mode=mode,
                  **kwargs)
        seglist = jieba.cut(value, cut_all=True)
        for w in seglist:
            t.original = t.text = w
            t.boost = 1.0
            if positions:
                t.pos = start_pos + value.find(w)
            if chars:
                t.startchar = start_char + value.find(w)
                t.endchar = start_char + value.find(w) + len(w)
            yield t

def ChineseAnalyzer():
    return ChineseTokenizer()
复制whoosh_backend.py文件修改为whoosh_cn_backend.py
sudo cp whoosh_backend.py whoosh_cn_backend.py
打开复制出来的新文件whoosh_cn_backend.py,引入中文分词类,内部采用Jieba分词
from .ChineseAnalyzer import ChineseAnalyzer
更改词语分词类
查找
analyzer=StemmingAnalyzer()
改为
analyzer=ChineseAnalyzer()
初始化索引数据
python manage.py rebuild_index
```

[支付宝支付配置]（非完整版，）

```
安装python-alipay-sdk
pip install python-alipay-sdk --upgrade
生成密钥文件
openssl
OpenSSL> genrsa -out app_private_key.pem   2048  # 私钥
OpenSSL> rsa -in app_private_key.pem -pubout -out app_public_key.pem # 导出公钥
OpenSSL> exit
在支付宝上下载的公钥是一个字符串，你需要在文本的首尾添加标记位

(-----BEGIN PUBLIC KEY-----和-----END PUBLIC KEY-----) 
=======
才能正常使用, 证书的格式你可以参考这里

初始化
from alipay import AliPay, ISVAlipay

app_private_key_string = open("/path/to/your/private/key.pem").read()
alipay_public_key_string = open("/path/to/alipay/public/key.pem").read()

app_private_key_string == """
    -----BEGIN RSA PRIVATE KEY-----
    base64 encoded content
    -----END RSA PRIVATE KEY-----
"""

alipay_public_key_string == """
    -----BEGIN PUBLIC KEY-----
    base64 encoded content
    -----END PUBLIC KEY-----
"""

alipay = AliPay(
    appid="",
    app_notify_url=None,  # 默认回调url
    app_private_key_string=app_private_key_string,
    # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
    alipay_public_key_string=alipay_public_key_string,
    sign_type="RSA" # RSA 或者 RSA2
    debug=False  # 默认False
)


#  如果您没有听说过ISV， 那么以下部分不用看了
# app_auth_code或app_auth_token二者需要填入一个
isv_alipay = ISVAliPay(
    appid="",
    app_notify_url=None,  # 默认回调url
    app_private_key_srting="",
    # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
    alipay_public_key_string="",
    sign_type="RSA" # RSA or RSA2
    debug=False  # False by default,
    app_auth_code=None,
    app_auth_token=None
)
```



- 项目启动：  
    - **注意: 项目启动前请先查看项目[配置环境文件](./configurationFile/images/fehelper-github-com-yuanwenq-dailyfresh-blob-dev-dailyfresh-settings-py-1544797232546.png),配置你相应的设置,并安装好各个环境,mysql+redis+nginx+fastDFS+celery等**
        ```
        项目包安装
        pip install -r requirements.txt
        
        Django启动命令
        python manage.py runserver 
        ```
- uwsgi web服务器启动：  
    - **注意: uwsgi开启需要修改[配置文件](./dailyfresh/settings.py)中的DEBUG和ALLOWED_HOSTS**
        ```    
        启动: uwsgi --ini 配置文件路径 / uwsgi --ini uwsgi.ini
        停止: uwsgi --stop uwsgi.pid路径 / uwsgi --stop uwsgi.pid
        ```
- celery分布式任务队列启动  
        ```
        celery -A celery_tasks.tasks worker -l info
        ```
- redis服务端启动  
        ```
        sudo redis-server /etc/redis/redis.conf
        ```
- FastDFS服务启动
        ```    
        Trackerd服务
        sudo /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start

        storge服务
        sudo /usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
        ```
- nginx
    ```
    启动nginx
    sudo /usr/local/nginx/sbin/nginx
    重启nginx
    sudo /usr/local/nginx/sbin/nginx -s reload
    ```
- 建立索引文件--搜索引擎  
  新环境需要配置jieba分词,生成[whoose_cn_backend]()文件
    ```
    python manage.py rebuild_index
    ```
- mysql事务隔离级别设置
    ```
    sudo vim /etc/mysql/mysql.conf.d/mysql.cnf
    transaction-isolation = READ-COMMITTED (读已提交)
    ```
## 项目包介绍
```
amqp==2.4.2
billiard==3.5.0.5
celery==4.2.1
certifi==2017.7.27.1
chardet==3.0.4
Click==7.0
crypto==1.4.1
Django==2.0.6
django-haystack==2.8.1
django-redis==4.10.0
django-redis-cache==2.0.0
django-redis-sessions==0.6.1
django-tinymce==2.8.0
fdfs-client-py==1.2.6
geetest==3.2.1
idna==2.6
itsdangerous==1.1.0
jieba==0.39
Jinja2==2.10
kombu==4.4.0
MarkupSafe==1.1.1
mutagen==1.42.0
mysqlclient==1.3.14
Naked==0.1.31
Pillow==5.4.1
pycryptodome==3.8.0
pycryptodomex==3.7.2
python-alipay-sdk==1.10.0
python-memcached==1.59
pytz==2017.3
PyYAML==5.1
redis==3.2.1
requests==2.18.4
shellescape==3.4.1
six==1.12.0
urllib3==1.22
vine==1.3.0
Werkzeug==0.14.1
Whoosh==2.7.4
```
## 注意点
pip install fdfs_client-py-master 存在bug，需要[下载特定版本](https://pan.baidu.com/s/1NkK7VbeNBrbTPUeTxcYD6A)  
redis版本需要2.10.6 否则会报错,因为使用django的版本过低问题  
如果使用乐观锁,需要修改mysql事务的隔离级别设置

## 总结：

经过此次项目实践，学会了许多技术点，比如：使用FastDFS分布式文件存储，Celery异步任务，全文检索引擎，同时也明白了团队交流、沟通的重要性。项目的部分功能未达到预期的效果，后期需要进行完善。

## 后期计划 ：

#### 一、完善添加项目功能

1.登录功能添加第三方登录
 2.商品模块中的商品列表使用ajax局部刷新及全文检索功能
 3.订单模块：
	1>添加订单状态
	2>物流信息查询
	3>快递运输选择
	4>完善第三方支付

#### 二、进行项目测试

#### 三、部署项目到服务器

#### 四、项目运行维护



## 