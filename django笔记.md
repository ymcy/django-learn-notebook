## Django介绍
##### By-刘艳斌
--------

1. 创建项目
    ```shell
    django-admin startproject <项目名>
    ```
2. 启动项目
    ```shell
    python3 manage.py runserver [ip:端口号]
    ```
## Django目录介绍
1. 目录结构
    ```shell
    .
    ├── db.sqlite3 #默认数据库文件
    ├── manage.py #管理文件
    └── mysite1 #应用
        ├── __init__.py #python默认包
        ├── settings.py #设置文件
        ├── urls.py #url主路由文件
        └── wsgi.py
    ```
2. settings.py设置项
    ```python
    #当前项目所在路径
    BASE_DIR 

    #启动模式 True调试模式 Fales上线模式
    #检测代码改动后，自动重启，报错页面会出现报错日志
    DEBUG 

    #请求头白名单
    ALLOWED_HOSTS 

    #配置应用
    INSTALLED_APPS

    #中间件  
    MIDDLEWARE

    #主路由文件位置
    ROOT_URLCONF

    #模板
    TEMPLATES

    #初始数据库
    DATABASES

    #默认语言，中文为 zh-Hans
    LANGUAGE_CODE

    #时间地区  可以改为 'Asia/Shanghai'
    TIME_ZONE


    ```
## url和视图

1. 主路由urls.py
    ```py
    urlpatterns =[] #路由路径名单
    # path参数
    from django.urls import path
    
    path(route,views,name = None)
    1.route:字符串类型,匹配的请求路径
    2.views:指定路径所对应的视图处理函数的名称
    3.name:为地址起别名，在模板中地址反向解析时使用

    path转换器
    path('xx/<int:page>',views.xxx) #匹配所有的 值为page，关键词传给视图函数
    ```
    | 转换器类型 | 作用                                                    |
    | ---------- | ------------------------------------------------------- |
    | str        | 匹配除了'/'之外的所有非空字符串                         |
    | slug       | 匹配任何由ASCII字母或数字以及连字符和下划线组成的短标签 |
    | path       | 匹配非空字段,包括路径分隔符'\'                          |


2. 视图函数语法
    ```py
    def xxx_view(request[,其他参数...]):
        ...
        return HttpResponse对象

    #样例 <项目同名文件下>/views.py
    from django.http import HttpResponse

    def page_2002(request):
        html = '<h1>这是第一个页面<h1>'
        return  HttpResponse(html)
    ```
3. re_path转换器
    * 在url的匹配过程中可以使用正则表达式进行精准匹配
    * 语法
        ```python
        from django.urls import re_path
        re_path(reg,view,name = xxx)
        #正则表达式位命令分组模式(?P<anme>pattern);
        #匹配提取参数后用关键字传参方式传递给视图函数
        ```
### 请求及响应
1. 请求的方法
    | 序号 | 方法    | 描述                                                                                                                                   |
    | ---- | ------- | -------------------------------------------------------------------------------------------------------------------------------------- |
    | 1    | GET     | 请求指定页面信息，并返回实体主体                                                                                                       |
    | 2    | HEAD    | 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头                                                                          |
    | 3    | POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件)。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。 |
    | 4    | PUT     | 从客户端向服务器传送的数据取代指定的文档的内容。                                                                                       |
    | 5    | DELETE  | 请求服务器删除指定的页面                                                                                                               |
    | 6    | CONNECT | HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。                                                                               |
    | 7    | OPTIONS | 允许客户端查看服务器的性能                                                                                                             |
    | 8    | TRACE   | 回显服务器收到的请求，主要用于测试或判断。                                                                                             |
2. Django中的请求
    * path_info : `URL字符串`
    * method : `字符串，表示HTTP请求方法，常用值：‘GET’，‘POST’`
    * GET：`QueryDict查询字典的对象，包含GET请求方式的所有数据`
    * POST：`QueryDict查询字典的对象，包含POST请求方式的所有数据`
    * FILES：`类似于字典的对象，包含所有的上传文件信息`
    * COOKIES：`Python字典，包含所有的cookie，键和值都为字符串`
    * session：`似于字典的对象，表示当前的会话`
    * body：`字符串，请求体的内容(POST或PUT)`
    * scheme：`请求协议('http'/'https')`
    * get_full_path():`请求的完整路径`
    * META: `请求中的元数据(消息头)`
        - META['REMOTE_ADDR']:`客户端的ip地址`
3. 常见的HTTP状态码：
    * 200 `请求成功`
    * 301 `永久重定向，资源被永久转移到其他的URL`
    * 302 `临时重定向`
    * 404 `请求的资源不存在`
    * 500 `内部服务器错误`
4. HttpResponse构造函数
    ```python
    HttpResponse(content = 响应体,
        content_type = 响应体数据类型,
        status = 状态码)
    ```
6. 常用的 Content_type如下
    * 'test/html' `默认的,html文件`
    * 'test/plain' `纯文本`
    * 'test/css' `css文件`
    * 'test/javasctipt' `js文件`
    * 'multipart/from-data' `文件提交`
    * 'application/json'` json传输`
    * 'application/xml' `xml文件`
7. 常见的响应体
    
    | 类型                    | 作用           | 状态码 |
    | ----------------------- | -------------- | ------ |
    | HttpResponseRedirect    | 重定向         | 302    |
    | HttpResponseNotModified | 未修改         | 304    |
    | HttpResponseBadRequest  | 错误请求       | 400    |
    | HttpResponseNotFound    | 没有对应的资源 | 404    |
    | HttpResponseForbidden   | 请求被禁止     | 403    |
    | HttpResponseServerError | 服务器错误     | 500    |
### Django设计模型及模板层
1. 传统的 MVC
    * M 模型层(Model):`主要用于对数据库层的封装`
    * V视图层(View):`用于向用户展示结果`
    * C控制(Controller):`用于处理请求、获取数据、返回结果(重要)`
    * 作用:`降低模块间的耦合度`
2. MTV代表
    * M 模型层(Model) `负责与数据库交互`
    * T 模板层(Template) `负责呈现内容到浏览器(HOW)`
    * V 视图层(View) `核心,负责接收请求、获取请求，返回结果(WHAT)`
    * 作用:`降低模块间的耦合度`
    * C是路由，被简化了
3. T 模板层
    * 模板是可以根据字典数据动态变化的HTML网页。
    * 模板是可以根据视图中传递的字典数据动态生成相应的HTML网页。
    * 使用
        ```python
        #创建模板文件夹<项目名>/templates
        #在 settings.py 中TEMPLATES配置项
        1. BACKEND:指定模板的引擎
        2. DIRS:模板的搜索目录
        3. APP_DIRS:是否要在应用中的templates文件夹中搜索模板文件
        4. OPTIONS：有关模板的选项
        #配置项中 需要修改部分
        设置DIRS-'DIRS':[os.path.join(BASE_DLR,'templates'))]
        ```
4. 模板的加载方式
    ```python
    #方案1
    from django.template import loader
    # 1.通过loader加载模板
    t = loader.get_template("模板文件名")
    # 2.将T转换成HTMl字符串
    html = t.render([字典数据])
    #3.用相应对象将转换的字符串内容返回给浏览器
    return HttpResponse(html)
    -------------------------------
    #方案2
    from django.shortcuts import render
    return render(request,'模板文件名',[字典数据])

    ```
5. 变量和标签
    ```python
    if 标签
    语法：  
    {% if 条件表达式1 %}
    {% elif 条件表达式2 %}
    {% else %}
    {%endif%}

    for

    {% for i in name %}
    <h1>{{ i }}</h1>
    {% empty %}#如果没数据
    {% endfor %}
    ```
### 过滤和继承
1. 过滤器
    ```python
    定义：在变量输出时对变量的值进行处理
    作用：可以通过使用过滤器来改变的输出显示
    语法：{{变量|过滤器1：'参数1'|过滤器:'参数2'}}
    ``` 
2. 常用过滤器 
    | 过滤器            | 说明                               |
    | ----------------- | ---------------------------------- |
    | lower             | 将字符串转为全部小写               |
    | upper             | 将字符串转换为大写形式             |
    | safe              | 默认不对变量内的字符串进行html转义 |
    | add:"n"           | 将value的值增加n                   |
    | truncatechars:'n' | 截断字符改为...结尾                |
    |cut:""|移除指定字符|
    |date:"Y/m/d"|将一个日期按照指定的格式，格式化成字符串|
    |first|返回列表/元组/字符串中的第一个元素|
    |last|返回列表/元组/字符串中的最后一个元素|
    |floatformat:3|使用四舍五入的方式格式化一个浮点类型|
    |join:"/" |类似与Python中的join|
    |length|获取一个列表/元组/字符串/字典的长度|
    |random|在被给的列表/字符串/元组中随机的选择一个值|
    |slice:"2:" |类似于Python中的切片操作|
    |stringtags|删除字符串中所有的html标签|
    |truncatechars:5 |类似于truncatechars，只不过是不会切割html标签|

3. 模板的继承
    ```python
    模继承可以使父模板中的内容重用，子模版直接继承父模板的全部内容并可以覆盖父模板中相应的块
    语法 - 父模板中：
    1.定义父模板中的块block标签
    2.标识出哪些在子模块中是允许被修改的
    3.block标签：在父模板中定义，可以在子模板中覆盖

    语法-子模板中：
    继承模板extends 标签(写在模板文件的第一行)
    例如{% extends'base.html' %}

    子模板 重写父模板中的内容块

    {% block block_name %}
    子模板块用来覆盖父模板中 block_name 块的内容
    {% endblock block_name %}
    ```

### URL反向解析
1. path
    ```python
    path(route,views,name = [别名])
    {%url '别名' %}
    {%url '别名' '参数1' '参数2' %}
    ex:
    {% url 'pagen' '400' %}
    {% url '400' age='18' name ='gxn' %}
    --------

    from django.urls import  reverse
    反向解析，可以用于302跳转
    reverse('别名','参数')
    示例:
    reverse('pagen',args = [300])
    reverse('pagen',kwargs = {'name':'xixi','age':18})
    ```

### 静态文件
1. 静态文件配置
    * 配置静态文件的访问路径【该配置默认存在】
        `STATIC_URL = '/static/'`
    * 指定访问静态文件时需要通过/static/XXX
        `http://127.0.0.1:8000/static/xxx`
    * 设置配置项   
        ```python
        STATICFILES_DIRS = (
        os.path.join(BASE_DIR,'static'),
        )
        ```
2. 访问方法
    * 方法1
        ```python
        #通过 
        #http://127.0.0.1:8000/static/[资源名]
        #/static/[资源名]
        ```
    * 方法2
        ```py
        #通过{% static %}标签访问静态文件
        #1. 加载static - {% load static %}
        #2. 使用静态资源 -{% static '静态资源路径' %}
        #3. 样例
            <img src="{% static 'imges/lena.jpg' %}">
        ``` 
### Django应用以及分布式路由
1. 什么是应用
    * 应用在Django项目中是一个独立的业务模块，可以包含自己的路由，视图，模板，模型
2. 创建应用
    * 用manage.py中的子命令startapp创建应用文件夹
        ```python
        python3 manage.py startapp [应用名字]
        ```
    * 在setting.py的INSTALLED_APPS列表中配置安装此应用
        ```python
        INSTALLED_APPS = ['...','应用名']
        ```
3. 什么是路由
    * Django中，主路由配置文件(urls.py)中可以不处理用户具体路由，主路由配置文件的可以做请求的分发(分布式请求处理)。具体的请求可以由各自的应用来进行处理
    * 配置分布式路由
        ```python
        #在[应用名]下创建urls.py
        from django.urls import include

        urlpatterns = [
            path(...),
            path('[应用名]/',include('应用名.urls')),
        ]
        ```
    * 应用下配置 urls.py
        ```python
        应用下创建urls.py文件，内容结构同主路由完全一样

        urlpatterns = [
            #http://127.0.0.1:8000/[应用名]/index
            path('index/',views.index_view)
        ]
        ```
4. 应用下的模板
    * 应用内部可以配置模板文件夹
    * settings.py中 开启 应用模板功能
        ```python
        TEMPLATE配置项中的“APP_DIRS”值为True即可
        ```
    * 应用下templates 和 外层templates都存在时，django得查找模板规则
        1. 优先查找外层templates目录下得模板
        2. 按INSTALLED_APPS配置下的 应用顺序 逐层查找
### 模型层及ORM介绍
1. 配置mysql
    ```python
    安装mysqlclient
    如果ubuntu，先执行
    $:apt-get install python3-dev default-libmysqlclient-dev

    ```
    进入数据库执行命令新建库
    ```sql
    create databases [数据库名] default charset set utf8
    CREATE DATABASE tedu_note  DEFAULT CHARACTER SET utf8;
    #通常数据库名字和项目名字保持一致
    ```
    settings.py里进行数据库的配置
    ```python
    #修改DATABASES配置项的内容，由sqlite3变为mysql
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',#数据库引擎
            'NAME': 'djangoapp',#数据库名
            'USER':'root',#用户名
            'PASSWORD':'root',#密码
            'HOST':"127.0.0.1",#主机名
            'PORT':'3306',#端口

                }
        }
    ```
2. 什么是模型
    * 模型是一个Python类，它是由django.db.models.Model派生出的子类
    * 一个模型类代表模型数据库中的一个表
    * 模型类中每一个类属性都代表数据库中的一个字段
    * 模型是数据交互的接口，是表示和操作数据库的方法和方式
3. ORM框架
    ```
    定义: ORM (object Relational Mapping)即对象关系映射,
    它是一种程序技术，它允许你使用类和对象对数据库进行操作,
    从而避免通过SQL语句操作数据库
    作用：
        1.建立模型类和表之间的对应关系，允许我们通过面向对象的方式来操作数据库。
        2.根据设计的模型类生成数据库中的表格。
        3.通过简单的配置就可以进行数据库的切换。
    ```
    | ORM  | DB     |
    | ---- | ------ |
    | 类   | 数据表 |
    | 对象 | 数据行 |
    | 属性 | 字段   |
4. 模型类代码
    ```python
    from django.db import models

    # Create your models here.

    class Book(models.Model):#表名Book
        title = models.CharField("书名",max_length=50,default='')#字符串 (admin名字,最大长度,默认值)
        #字段 字段title
        #admin后台查看数据是书名
        #max_length：最大长度50
        #default：默认值''
        price = models.DecimalField('定价',max_digits=7,decimal_places=2,default=0.0)#字符串 (admin名字,总精度,小数点，默认值)
        #字段 price
        #admin：后台查看数据是定价
        #max_digits：最大精度
        #decimal_places：小数点位数
        #default：默认值

    ```
5. 数据库迁移
    ```shell
    #生成迁移文件
    $:python3 manage.py makemigrations
    #将应用下的models.py文件生成一个中间的文件，并且保存在migrations文件夹中
    #执行迁移文件
    $:python3 manage.py migrate
    #执行迁移程序实现迁移。将每个应用下的migrations目录中间文件同步回数据库

    ```
6. 创建一个后台管理员
    ```python
    python3 manage.py createsuperuser
    #注册到admin

    #应用名/admin.py
    from django.contrib import admin

    from . import  models

    admin.site.register(models.Book)



    ```
### 基础字段及选项
1. 字段类型
    * BooleanField()
        * 布偶类型
        * 数据库类型:`tintint(1)`
        * 在编程语言中:`使用True或False来表示值`
        * 在数据库中:`使用0或1来表示具体的值`
    * CharField()
        * 数据库类型:`varchar`
        * 注意:`必须指定max_length参数值`
    * DateField()
        * 数据库类型:`date`
        * 作用:`表示日期`
        * 参数:
            1. auto_now:`每次保存对象时，自动设置该字段为当前时间（True,False）`
            2. auto_now_add:`当对象第一次被创建时自动设置当前时间(True,False)`     
            3. default:`设置当前时间(取值:字符串格式时间如：'2019-6-1')`
            * 以上参数只能三选一
    * DateTimeField()
        * 数据类型:`datetime(6)`
        * 作用: `表示时间和日期`
        * 参数同DateField
    * FloatField()
        * 数据类型:`double`
        * 编程语言中和数据库中都使用小数值表示
    * DecimalField()
        * 数据库类型:decilmal(x,y)
        * 编程语言中:`使用小数表示该列的值`
        * 在数据库中:`使用小数`
        * 参数
            1. max_digits:`位数总数`
            2. decimal_places:`小数点后的数字数量`
    * EmailField()
        * 数据类型:`varchar`
        * 编程语言和数据库中使用字符串
    * InttegerField()
        * 数据库类型:`int`
        * 编程语言和数据库中使用整数
    * ImageField()
        * 数据库类型:`varchar(100)`
        * 作用:`在数据中为了保存图片的路径`
        * 编程语言和数据库中使用的字符串
    * TestField()
        * 数据库类型:`longtext`
        * 作用:`表示不定长的字符数据`
    * AutoField()
        * 数据框类型:`int`
        * 作用:`自增长整数`
    * DecimlField
        * 数据库类型:`double`
        * 作用:`双精度小数`
    * TimeField
        * 作用:`时间`
    * DateTimeField 
        * 作用:`时间和日期`
    * URLField
    * IPAddressField
    * ImageFiled
    * FileFiled
* 字段选项
    * primary_key
        * 如果设置为True,表示该列为主键，如果指定一个字段为主键，则数据库表不会创建id字段
    * blank
        * 设置为True时，字段可以为空。设置为False时，字段是必须填写的
    * null
        * 如果设置为True，表示该列值允许为空。
        * 默认为False，如果此选项为False建议加入Default选项来设置默认值
    * default
        * 设置所所在列的默认值，如果字段为null=False建议添加此项
    * db_index
        * 如果设置为True，表示为该列增加索引
    * unique
        * 如果设置为True，表示该字段在数据库中的值必须是唯一的
    * db_column
        * 指定列的名字，如果不指定的话，采用属性名作为列名
    * verbose_name
        * 设置此字段在admin界面上的显示名称
    * autoincrement
        * 自增长
    * choices
        * 元组类别，一个用来选择值的二维元组，第一个实际存储的值，第二个用来方便进行选择。sex_CHOICES=((1,'男'),(0,'女'))
    * error_messages
        * 返回错误信息
* 模型类
    * Meta类
        ```python
        使用内部Meta类来给模型赋予属性，Meta类下有很多内建的类属性，可对模型类做一些控制
        from django.db import models

        class Book(models.Model):
            title = models.CharField("书名",max_length=50,default='')
            price = models.DecimalField('定价',max_digits=7,decimal_places=2,default=0.0)
            info = models.CharField('描述',max_length=100,default='')
            class Meta:
                #可以改变当前模型类对应的表名
                db_table = 'book'

        ```
### ORM基本操作
* 创建数据
    * 管理器对象
        ```python
        #每个继承自models.Model的模型类，都会有一个objects对象被同样继承下来，
        #这个对象叫管理器对象

        #数据库的增删改查可以通过模型的管理器实现

        class MyMosql(models.Model):
            ...
        Mymodel.objects.create(属性1 = 值1) #创建数据
        #成功：返回创建好的实体对象
        #失败：抛出异常

        ```
    * 创建数据
        ```python
        方法1
        Mymodel.objects.create(属性1 = 值1) #创建数据
        #成功：返回创建好的实体对象
        #失败：抛出异常

        方法2
        #创建Mymodel实例对象，并调用save保存
        obj = MyModel(属性 = 值)
        obj.属性 = 值
        obj.save()
        ```
* 查询数据
    * 数据库的查询需要使用管理器对象进行
    * 通过MyModel.objects 管理器方法调用查询方法
        | 方法      | 说明                               |
        | --------- | ---------------------------------- |
        | all()     | 查询全部记录，返回QuerySet查询对象 |
        | get()     | 查询复合条件的单一记录             |
        | filter()  | 查询符合条件的多条记录             |
        | exclude() | 查询符合条件之外的全部记录         |
    * all()方法
        ```python
        #用法MyModel.objects.all()
        #作用:查询MyModel实体中所有的数据
        #等同于select * from table
        #返回值:QusrySet容器对象,内部存放MyModel实例

        from bookstore.models import Book
        books = Book.objects.all()
        for book in books:
            print("书名",book.title,'出版社:',book.pub)

        #重写__str__方法获取更全提示
        def __str__(self):
            return '{}_{}_{}_{}'.format(self.title,self.price,self.pub,self.market_price)
        ```
    * values('列1','列2')
        ```python
        #用法:MyModel.objects.values(...)
        #作用:查询部分列的数据并且返回
        #等同于 select 列1,列2 from xxx
        #返回值: QuerySet
            #返回查询结果容器,容器内存字典，每个字典代表一条数据，格式为{'列1':值1,'列2':值2}
        ```
    * values_list('列1','列2')
        ```python
        #用法:MyModel.objects.values(...)
        #作用:返回元组形式的查询结果
        #等同于 select 列1,列2 from xxx
        #返回值: QuerySet,内部存放元组
        ```
    * order_by()
        ```python
        #用法:MyModel.objects.order_by('[-]列','列')
        #对字段进行排序,返回all()类似
        #降序前面加 - 
        ```
    * 可以使用多个查询组合
        ```python
        MyModel.objects.values(...).order_by('[-]列','列')
        ```
    * 查看sql语句
        ```python
        #返回的QuerySet对象.query
        book = Book.objects.order_by('title')
        book.query
        ```
* 条件查询
    * filter(条件)  
        ```python
        #语法:MyModel.objects.filter('属性1' = 值1','属性2' = '值2')
        #作用:返回包含此条件的全部的数据据
        #返回值:
            # QuertSet容器对象
            #与查询
        Book.objects.filter(title = '海底两万里')

        ```
    * exclude(条件)
        ```python
        #语法:MyModel.objects.exclude(条件)
        #返回不包含此条件的全部的数据集
        ```
    * get(条件)
        ```python
        #语法:MyModel.objects.get(条件)
        #返回满足条件的唯一一条数据
        #该方法只能返回一条数据
        #查询结果多余一条数据则抛出
        Model.MultipleObjectsReturned异常
        #查询结果没有数据抛出
        Model.DoesNotExist异常
        ```
* 查询谓词
    ` filter(id__exact = 1)`
    | 词           | 含义                           | 备注     |
    | ------------ | ------------------------------ | -------- |
    | __exact      | 等值匹配                       |          |
    | __contains   | 包含指定值                     |          |
    | __startswith | 以XXX开始                      |          |
    | __endswith   | 以XXX结束                      |          |
    | __gt         | 大于指定值                     |          |
    | __gte        | 大于等于                       |          |
    | __lt         | 小于                           |          |
    | __lte        | 小于等于                       |          |
    | __in         | 查询数据是否在指定范围内       | ['a',b'] |
    | __range      | 查询数据是否在指定的区间范围内 | (35,50)  |
    | __isnull     | 是否为空                       | True     |
    |__startswith|匹配字符串开头||
    |__endswith|匹配字符串结尾||
    |__range|范围测试|(1,5)|
    |__data|查找datatime字段|datetime(2019,5,8)|
    |__time|查找datatime字段|datetime(10,30|
    |__regex|正则匹配|\^[a-z]|
    |__exact|完全符合||
    
* 更新操作
    * 修改单个实体的某些字段值的步骤:
        1. 查 get()
        2. 改 对象.属性
        3. 保存 对象.save()
    * 批量更新
        ```python
        #直接调用QuerySet的update(属性 = 值)实现批量修改
        #将 id大于3的所有图书定位0元
        books = Book.objects.filter(id__gt = 3)
        books.update(price = 0)
        ```
* 删除操作
    * 单个数据删除
        ```python
        #步骤
        #    1.查找查询结果对应的一个数据对象
        #    2.调用这个数据对象的delete()方法实现删除
        #    3.
        try:
            auth = Author.objects.get(id = 1)
            ath.delete()
        except:
            print("删除失败")
        ```
    * 批量数据
        ```python
        auth = Author.objects.filter(id = 1)
        ath.delete()
        ```
    * 伪删除
        ```python
        #通常不会轻易在业务里把数据真正删掉，取而代之的是做伪删除，即在表中添加一个布尔型字段(is_active)
        #,默认是True;执行删除时，将欲删除的is_active字段设置为Flase

        #注意 伪删除，显示数据的地方，均添加 is_active = True过滤
        ```
* F对象和Q对象
    * F对象
        ```python
        #一个F对象代表数据库中某条记录的字段的信息
        #作用:
            #通常是对数据库中的字段值在不获取的情况下进行操作
            #用于类属性之间的比较
        #语法
        from django.db.models import F
        F('列名')

        #示例1 更新Book实例中所有的零售价涨10元
        Book.objects.all().update(market_price = F('market_price') + 10)

        #以上做法好于如下代码
        #books = Book.objects.all()
        #for book in books:
        #    book.market_price = book.market_price + 10
        #    book.save()

        #F可以解决大量数据来的资源竞争

        #实例2 对数据库的值进行比较，列出哪些书的零售价高于定价
        books = Book.objects.filter(market_price__gt = F('price'))
        ```
    * Q对象 
        ```python
        #当在获取查询结果集 使用复杂的逻辑或 |，逻辑非~等操作时，可以借助Q对象进行操作
        #如果找出定价低于20元 或 清华大学出版社的全部书，可以写成
        from django.db.models import Q
        Book.objects.fiter(Q(price__lt = 20) | Q(pub = "清华大学出版社"))
        #在条件中 使用与&或|非~查询，以及组合

        ```
* 聚合查询和原生数据库操作
    * 聚合查询
        ```python
        聚合查询是指对一个数据表中的一个字段的数据进行部分或全部进行统计查询,查bookstore_book数据表中的全部书的平均价格，查询所有书的总个数等,都要使用聚合查询
        聚合查询分为
            √整表聚合
            √分组聚合
        ```
    * 整表查询
        ```python
        #不带分组的聚合查询是指导将全部数据进行集中统计查询
        #聚合函数[需要导入]:
        #导入方法: 
        from django.db.models import *
        #- 聚合函数: 
        # Sum,Avg, Count, Max,Min
        #语法:
        MyModel.objects.aggregate(结果变量名=聚合函数('列'))
        #-返回结果:结果变量名和值组成的字典
        #格式为:
            {"结果变量名":值}
        ```
    * 分组聚合
        ```python
        #分组聚合是指通过计算查询结果中每一
        #个对象所关联的对象集合,
        #从而得出总计值(也可以是平均值或总和)，即为查询集的每一项生成聚合。
        #语法:
        models.Book.objects.values('pub')
        QuerySet.annotate(结果变量名=聚合函数('列'))
        #返回值:
        OuerySet
        ```
* 原生数据操作
    ```python
    #Django也可以支持直接用sql语句的方式通信数据库
    #查询:使用MyModel.objects.raw()进行数据库查询操作查询语法:MyModel.objects.raw(sql语句,拼接参数)
    #返回值: RawQuerySet集合对象【只支持基础操作,比如循环】
    books = models.Book.objects.raw( ' select * from bookstore_book ',(['拼接参数'],))
    for book in books:
        print(book)
    ```

    * 游标 connection
        ```python
        from django.db impoer connection
        2.用创建cursor类的构建函数创建,常使用with语句进行创建为保证在出现异常时能释放cursor资源,通
        from django.db import connection
        with connection.cursor() as cur:
            cur.execute('执行SQL语句','拼接参数')
        ```
### 关系映射
*  一对一
    *  一对一
        ```python
        #一对一是表现现实事物间存在的一对一的对应关系
        #语法 OneToOneField(类名,on_delete = xxx) 跟谁关联,级联删除
        on_delete：
            models.CASCADF 级联删除。"共生死"
            models.PROTECT 抛出ProtectedError,阻止删除
            SET_NULL 设置ForeignKey null,null = True
            SET_DEFAULT 将ForeignKey设置为其默认值
        class A(model.Model):
            ...
        class B(model.Model):
            属性 = model.OneToOneField(A,on_delete = xxx)
        ```
   * 创建一对一数据
        ```python
        #无外键的模型类
        author1 = Author.objects.create(name = '王老师')
        #有外键的模型类
        wife1 = Wife.objects.create(name = '王夫人',author = author1)
        wife1 = Wife.objects.create(name = '王夫人',author_id = 1)
        ```
    * 正向查询
        ```python
        #正向查询:直接通关外键属性查询，则称为正向查询
        #通过wife找author
        from .models import wife
        wife = wife.objects.get(name='王夫人')
        print(wife.name,'的老公是',wife.author.name)
        ```
    * 反向查询
        ```python
        au = Author.objects.get(name = '王先生')
        au.wife
        ```
* 一对多
    * 一对多
        ```python
        class Publisher(models.Model):
            """出版社[1]"""
            name = models.CharField('名称',max_length=50,unique=True)

        class Book(models.Model):
            """书"""
            title = models.CharField('书名',max_length=50)
            publisher = models.ForeignKey(Publisher,on_delete=models.CASCADE)
        ```
    * 正向查询
        ```python
        ...
        ```
    * 反向查询
        ```python
        pub1 = Publisher.objects.get(name = '新华书店')
        pub1.book_set.all()
        ```
* 多对多
    * 多对多
        ```python
        在关联的两个类中任何一个类中，增加:
        属性 = models.ManyToManyField(MyModel)
        ```
    * 正向查询
        ```python
        boo = Book.objects.get(bookname = '狂人日记')
        boo.author.all()
        ```
    * 反向查询
        ```python
        au = Author.objects.get(name = '周树人')
        au.book_set.all()
        ```
### admin管理后台
* admin配置步骤
    ```shell
    $: python3 managepy createsuperuser #创建一个超级用户
    Username:#输入用户名
    Email address:#输入邮箱
    password: #此处数据密码
    password(again):#再次输入密码
    ```
* 注册自定义模型类
    ```python
    #注册步骤
        #1 在应用app中的admin.py中导入注册要管理的模型类models类，如:
        from .models import Book
        #2 调用admin.site.register方法进行注册,如
        admin.site.register(自定义模型类)

    ```
* 模型管理器类
    ```python
    #作用
    #   为后台管理界面田间便于操作的新功能
    #说明
    #   后台管理器类须继承自django.contrib.admin里的ModelAdmin类
    #使用方法
    #1.在<应用app>/admin.py里定义模型管理器类
    class XXXXManager(admin.ModelAdmin):
        ...
    #2.绑定注册模型管理器和模型的类
    from django.contrib import admin
    from .models import *
    admin.site.register(YYYY,XXXXManager)
    #绑定YYYY模型类与管理器类XXXXManager

    #如 
    class BookManager(admin.ModelAdmin):
        #列表显示哪些字段的列
        list_display = ['id','title','price','market_price']
        #控制list_display中的字段，哪些可以连接到修改页
        list_display_links = ['title']
        #添加过滤器
        list_filter = ['pub']
        #添加搜索框 模糊查询
        search_fields = ['title']
        #添加可在列表中编辑的字段
        list_editable = ['price']
        #详情页显示的字段
        fields = []
        # 只允许读取的字段
        readonly_fields = []


        
    admin.site.register(Book,BookManager)
    ```
* Meta类
    ```python
    通过Meta内嵌类定义模型的属性，用法如下：
    class Book(models.Model):
        title = CharField(....)
        class Meta:
            db_table = '数据库表名'#需要迁移数据库
            verbose_name = '单数名'
            verbose_name_plural = '复数名'
    ```
### 会话
* Cookies
    ```text
    Cookies和Session就是为了保持会话状态而诞生的两个存储技术
    Cookies在浏览器以键值保持的
    存储的数据类型带有生命周期
    cookies中的数据按域存储隔离的
    cookies的每內部数据会在每次访问此网址时都会携带到服务器段
    ```
* cookies存储
    ```python
    HttpRespons.set_cookie(
        key, #cookie的名字
        value,#cookie的值
        max_age = None,#cookie存活时间，秒为单位
        expires = None#具体过期时间
    )#当不指定max_age和expires时，关闭浏览器时，数据失效

    #添加或修改cookie
    responds = HttpResponse('已添加...')
    responds.set_cookie('my_var1',123,3600)
    return responds

    #删除cookies
    HttpResponse.delete_cookie(key)
    #获取Cookies
    request.COOKIES.get('key')

    ```
* session
    ```python
    settings.py中配置session
    #1.向INSTALLED_APPS列表中添加:
    INSTALLED_APPS = [
        #启动sessions应用
        'django.contrib.sessions'
    ]
    #2.向 MIDDLEWARE列表中添加
    MIDDLEWARE = [
        #中间件
        'django.contrib.sessions.middleware.SessionMiddleware'
    ]
    

    ------------
    #保存session的值到服务器
    request.session['KEY'] = VALUE
    #获取session的值
    value = request.session.get('key')
    #删除
    del request.session['KEY']

    ------
    #相关配置
    #指定sessionid在cookies中保存的时长
    SESSION_COOKIE_AGE = 60 * 60 * 24 * 7 * 2

    #浏览器关闭时，session失效
    SESSION_EXPIRE_AT_BROWSER_CLOSE = True #默认Fales
    #清过期数据
    $ python3 manage.py clearsessions
    ```
### 缓存
1. 缓存的定义
    定义:缓存是一类可以更换读取数据介质的统称，也可以加快读取速度
    意义：防止数据库的频率查询过高
2. 缓存到数据库配置
    ```python
    CACHES = {
        'default': {
            'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
            'LOCATION': 'my_cache_table',
            'TIMEOUT': 300,  # 缓存保存的时间 单位秒，默认值为300
            'OPTIONS': {
                'MAX_ENTRIES': 300,  # 缓存最大数据条数
                'CULL_FREQUENCY': 2,  # 缓存条数达到最大值时 刷出 1/x的数据
                }
            }
        }
    $:python3 manage.py createcachetable
    ```
3. 缓存到内存
    ```python
        CACHES = {
        'default':{
            'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
            'LOCATION':"unique-snowflake" #存取算法
            }
        }

    ```
4. 缓存到本地
    ```python
        CACHES = {
        'default': {
            'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
            'LOCATION': os.path.join(BASE_DIR, 'django_cahce')
            }
        }
    ```
5. 整体缓存策略
    ```python
    from django.views.decorators.cache import cache_page

    #视图函数中使用
    @cache_page(30) #单位s,生效时间
    def my_views(request):
        ...

    #路由中使用
    urlpatterns = [
        path('foo/',cache_page(60)(views.my_view))
    ]
    ```
6. 局部缓存
    ```python
    from django.core.cache import cache #导入模块
    def all_book(request): #视图函数
        CACHE = cache.get('all_book') #获取cache
        if not CACHE: #如果没有得到
            CACHE = models.Book.objects.all() #访问函数后，获得对象，赋值
            cache.set('all_book', CACHE,5) #写入cache
        return render(request, 'all_book.html', {'data': CACHE})

    ```
    | 方法        | 参数               | 用处                            | 返回值                           |
    | ----------- | ------------------ | ------------------------------- | -------------------------------- |
    | set         | key,values,timeout | 存取一个值                      |                                  |
    | get         | key                | 获取一个值                      | 获取成功返回key的值,失败返回NOne |
    | add         | key,value          | 存储缓存，只在key不存在时候有效 | True(成功)False(失败)            |
    | get_or_set  | key,value,timeout  | 存储或者输出缓存                | value                            |
    | set_many    | dict,timeout       | 批量存储                        | 插入不成功的数组                 |
    | get_many    | key_list           | 批量获取缓存数据                | 取到的key和value字典             |
    | delete      | key                | 删除key的缓存数据               |                                  |
    | delete_many | key_list           | 批量删除                        |                                  |

### 浏览器缓存
1. 强缓存
    * 响应体 - Expires
        ```
        定义：缓存过期时间,用来指定资源到期的时间，是服务器的具体的时间点
        样例：Expires:Thu,02 Apr 2023 05:14:08 GMT
        ```
    * 响应头 Cache-control
        ```
        用于控制网页缓存。比如当Cache-Control:max-age = 150 代表请求创建后的120秒，缓存失效
        ```
    3. 浏览器优先使用Cache-control
2. 协商缓存
    ```
    强缓存中的数据一旦过期，还需要跟服务器进行通信，从而获取最新数据;思考?如果强缓存的数据是一些静态文件，大图片等;
    解答:考虑到大图片这类比较费带宽且不易变化的数据，强缓存时间到期后，浏览器会去跟服务器协商，当前缓存是否可用，如果可用，服务器不必返回数据，浏览器继续使用原来缓存的数据，如果文件不可用，则返回最新数据
    ```
    1. Last-Modified
        ```python
        文件最近的修改时间,如果浏览器访问服务器返回Last-Modified响应头,则代表该资源为需协商的缓存
        ```
    2. if-Modified-Since
        ```python
        当缓存过期后，浏览器将获取到的Last-Modified值作为请求头的值，与服务器协商，服务器返回304代表缓存继续可用，200代表缓存不可用
        ```

    3. ETag相应头和if-None-Match请求头
        ```
        不是依赖修改时间，而是依赖文件标识
        ```
---
### 中间件
1. 编写中间件
    ```python
    中间件类须继承自django.utils.deprecation.MiddlewareMixin类

    #请求过滤
    process_request(self,request) 
    #执行路由前被调用，在每个请求上调用，返回None或者HttpResponse对象
    #：return  None 继续执行
    #          HttpResponse 被禁止
    

    #在达到视图函数之前 可以替换视图函数 参数
    process_view(self,request,callback,callback_args,callback_kwarhs)
    #调用函数前被调用，在每个请求上调用，返回None或HttpResponse对象
    #request 请求
    #callback 视图函数
    #callback_args 视图函数位置传参
    #callback_kwarhs 视图函数关键字传参
    #：return  None 继续执行
    #          HttpResponse 被禁止


    #可以查看响应和替换响应体
    process_response(self,request,response)
    #所有响应返回浏览器被调用，在每个请求上调用，返回HttpResponse对象
    #response 响应

    #可以保存日志
    process_exception(self,request,exception)
    #当处理过程中抛出异常时调用，返回一个HttpResponse对象


    process_template_response(self,request.reponse)
    #在视图函数执行完毕返回的对象中包含render方法时被调用;该方法需要返回render方法的响应对象
    ```
2. 注册中间件
    ```python
    #file: settings.py

    MIDDLEWARE = [
        ...
    ]
    #配置为数组,中间件被调用时以 进入：先上到下，出去：再由下到上
    ```
### CSRF防范
1. 配置
    ```python
    #csrf是伪造跨站请求
    #1
    settings.py中确认MIDDLEWARE中
    dango.middleware.csrf.CsrfViewMiddleware是否打开
    #2
    #模板中中,from 标签下添加如下标签
    {%  csrf_token %}


    #局部关闭CSRF防范
    from django.views.decorators.csrf import csrf_exempt

    @csrf_exempt
    def my_view(request):...
    ```
### 分页
1. 分页
    ```
    分页是指在web页面有大量数据需要显示
    为了方便在每个页面中只显示部分数据
    1.方便阅读
    2.减少数据提取量,减轻服务器压力
    ```
2. 使用paginator
    ```python
    #导入
    from django.core.paginator import Paginator

    paginator = Paginator(object_list,per_page)
    #object_list 需要分类数据的对象列表
    #per_page    每页数据个数
    #返回值      Paginator对象

    属性:
        count: 需要分页数据的对象总数
        num_pages: 分页后的页面总数
        page_range: 从1开始的range对象,用于记录当前面码数
        per_page:每页数据个数
    方法:
        def paginator.page(number):
            参数 number为页码信息(从1开始)
            返回当前number页对应的页信息
            如果提供的页码不存在，抛出lnvalidPage异常
                - PageNotAnlnterget： 传入不是整数
                - EmptyPage：当前页面没数据
    page()方法 返回Page对象
    page = paginator.page(number)
    属性:
        - object_list:当前页上所有数据对象的列表
        - number:当前页的序号,从1开始
        - paginator:当前page对象相关的Paginator对象
    方法:
        - has_next() 如果有下一页返回True
        - has_previous() 如果有上一页返回True
        - has_other_pages() 如果有上一页或下一页返回True
        - next_page_number()返回下一页页码，不存在抛出lnvalidPage异常
        - previous_page_number()返回下一页页码，不存在抛出lnvalidPage异常
    ```
3. 使用
    ```python
    from django.core.paginator import Paginator

    def test_page(request):

        page_num = request.GET.get('page',1) #获取页码
        all_data = ['a','b','c','d','e'] #假设数据列表
        #初始化paginator
        paginator = Paginator(all_data,2)
        #初始化 具体页码对象
        c_page = paginator.page(int(page_num))

        return render(request,'test_page.html',locals())
    ```
    ```html
    <!-- 获取当前页面数据 -->
    {% for i in c_page %}
        <p>{{ i }}</p>
    {% endfor %}

    <!-- 上一页 -->
    {% if c_page.has_previous %}

        <a href='/book/test_page?page={{  c_page.previous_page_number }}'>上一页</a>
    {% else %}
        上一页
    {% endif %}

    <!-- 下一页 -->
    {% if c_page.has_next %}
        <a href='/book/test_page?page={{ c_page.next_page_number }}'>下一页</a>

    {% else %}
        下一页
    {% endif %}

    <!-- 所有页码 -->

    {% for p_num in paginator.page_range %}
        {% if p_num == c_page.number %}
            {{ p_num }}
        {% else %}
            <a href="/book/test_page?page={{ p_num }}">{{ p_num }}</a>
        {% endif %}
    {% endfor %}
    ```
### CSV
1. 下载csv
    ```python
    - 响应 Content-Type类型需要修改为text/csv
        这告诉浏览器该文档是csv文件，而不是html文件
    - 响应会获得一个额外的Content-Dispostion标头，其中包含
        csv文件的名称。
    ```
2. 使用
    ```python
    def make_csv(request):
        response = HttpResponse(content_type='text/csv')
        response['Content-Disposition'] = 'attachment;filename = "mybook.csv"'#设置为下载,文件名
        books = models.Book.objects.all()

        writer = csv.writer(response)
        writer.writerow(['书名','出版社','定价','图书零售价','描述'])
            for i in books:
            writer.writerow([i.title,i.pub,i.price,i.market_price,i.info])
        return response
    ```
### 内建用户系统
1. 定义
    ```python
    Django带有一个用户认证系统。它处理用户账号、组、权限以及基于cookie的用户会话。
    用户可以直接使用Django自带的用户表官方文档:https://docs.diangoproiect.com/en/2.2/topics/auth/
    ```
2. 基本字段
    ```python
    模型类位置 from django.contrib.auth.models import User
    ```
    |            |        |
    | ---------- | ------ |
    | username   | 用户名 |
    | password   | 密码   |
    | email      | 邮箱   |
    | first_name | 名     |
    | last_name  | 姓     |
    |is_superuser|是否是管理员账号(/admin)|
    |is_staff|是否可以访问admin管理界面|
    |is_active|是否为活跃用户，默认True。假删除|
    |last_login|上一次的登录时间|
    |date_joined|用户创建的时间|
3. 创建用户
    1. 创建普通用户create_user
        ```python
        from django.contrib.auth.models import User
        user = User.objects.create_user(username = '用户名',password = '密码',emil = '邮箱',...)
        ```
    2. 创建超级用户create_superuser
        ```python
        from django.contrib.auth.models import User
        user = User.objects.create_superuser(username = '用户名',password = '密码',emil = '邮箱',...)
        ```
    3. 校验密码
        ```python
        from django.contrib.auth import authenticate

        user = authenticate(username = username,password =password )
        ```
    4. 修改密码
        ```python
        from django.contrib.auth.mode1s import User
        try :
            user = User.objects.get(username= 'xiaonao ')user.set_password( '654321')
            user.save()
            return HttpResponse("修改密码成功! ")
        except :
            return HttpResponse("修改密码失败! ")
        ```
    5. 登录状态的保持
        ```python
        from django.contrib.auth.models import login

        def login_view(request):
            user = authenticate(username = username,password =password )
            login(request,user)
        ```
    6. 登录状态的校验
        ```python
        from django.contrib.auth.decorators import 
        login_required
        @login_required
        def index_view(request):
            #该视图必须为用户登录状态下才可访问
            #当前登陆用户可通过request.user获取
            login_user = request.user
        ```
        ```python
        #settings.py
        LOGIN_URL = '/login'
        #未登录下跳转
        ```
    7. 登录状态的取消
        ```python
        from django.contrib.auth.models import logout
        def logout_view(request):
            logout(request)
        ```
    8. 拓展字段
        ```python
        1. 通过建立新表，跟内建表做1对1
        2. 继承 内建的抽象user模型类
        #步骤
            - 添加新的应用
            - 定义模型类 继承 AbstractUser
            - settings.py中指明AUTH_USER_MODEL = '应用名.类名'
        ```
### uwsgi
1. 基础概念
   1. 在安装机器上安装和配置相同版本的环境[py,数据库等]
   2. django项目迁移
    ```shell
    $:sudo scp [本机目录] root@[远程地址]:[远程目录]
    ```
   3. 用uWSGI代替 python3 manage.py runserver方法启动服务器
   4. 配置nginx反向代理服务器
   5. 用nginx配置静态文件路径，解决静态路径问题
2. uWSGI定义
    ```text
   uwSGI是wSGl的一种,它实现了http协议 wSGI协议以及uwsgi协议uWSGI功能完善，支持协议众多，在python web圈热度极高
    uWSGI主要以学习配置为主
    ```
3. 配置
    ```
    安装`pip3 install uwsgi==2.0.19`

    添加配置文件 项目同名文件夹/uwsgi.ini
    文件以`[uwsgi]`开头，有如下配置项:
    套接字方式[需要有nginx]
        `socket=127.0.0.1:8000`
    Http通信方式
        `http=127.0.0.1:8000`
    项目当前工作目录
        `chdir=[目录]`
    项目中wsgi.py文件的目录，相对于当前工作目录
        `wsgi-file=my_project/wsgi.py`
    进程个数
        `process=4`
    每个进程的线程个数
        `threads=2`
    服务器的pid记录文件
        `pidfile=uwsgi.pid`
    服务器的日志文件位置
        `daemonize=uwsgi.log`
    开启主进程管理模式
        `master=true`
    ```
4. 启动
    ```shell
    启动
    uwsgi --ini uwsgi.ini
    重启
    uwsgi --reload uwsgi.pid
    关闭
    uwsgi --stop uwsgi.pid
    ```
5. 常见问题
    ```
    1，启动失败:端口被占用
    原因:有其他进程占用uwSGI启动的端口;
    解决方案:可执行sudo lsof -i:端口号查询出具体进程;杀掉进城后，重新启动uWSGl即可
    2，停止失败: stop无法关闭uWSGl
    原因:重复启动uWSGl,导致pid文件中
    解决方案: ps出uwWSGI进程，手动kill掉
    ```

------
### 创建一个用于生产的服务器
1. 创建项目
2. 更改`BASE_DIR`目录,`LANGUAGE_CODE = 'zh-Hans'`,`TIME_ZONE = 'Asia/Shanghai'`
3. 创建本地与生产环境的`settings.py`,如`settings_dev.py`,并且设置环境变量`DJANGO_SETTINGS_MODULE=bws.settings_dev`
4. 生成 `requirements.txt`,`.gitignore`