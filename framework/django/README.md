[TOC]



# 准备

安装

`pip install Django`



下载包到本地安装

`git clone https://github.com/django/django.git`

`pip install -e django/`



`python -m django --version`   查看版本



# 一、创建项目



### 创建项目

##  

`django-admin startproject sitename`



`vi sitename/settings.py`    配置，也可以后面配置



````
cd sitename/							# 进入项目目录
python manage.py runserver 0.0.0.0：8000		# 启动

数据库变更

python3 manage.py makemigrations # 生成变更程序
python3 manage.py migrate		# 执行变更，同步数据模型
````



### 创建app 

`python manage.py startapp appname`



```
vi sitename/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'appname',  # 添加导入刚才创建的app
]
```





# 二、配置与全局



```
vi sitename/settings.py

SECRET_KEY = 'e(!)-kb_kqw5p@%u!6ak6u2$db5-4h@c#u)dwrcw^xp5m-q(q$'

# 调试模式开启
DEBUG = True

# 站点访问域名,* 为不限
ALLOWED_HOSTS = [
    '*'
]


# Application definition,要添加导入的app模块

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app01', # 自己创建的要后面加上
    'app02'
]

#　过滤器
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'System.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'System.wsgi.application'


# Database
# https://docs.djangoproject.com/en/2.1/ref/settings/#databases

# DATABASES = {
#     'default': {
#         'ENGINE': 'django.db.backends.sqlite3',
#         'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
#     }
# }

# 必须配置项mysql
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'HOST': '10.0.1.5',
        'NAME': 'forumbbs',    #你的数据库名称
        'USER': 'itcp',   #你的数据库用户名
        'PASSWORD': 'Sime@903',
        'PORT': '3309',
    }
}


# Password validation
# https://docs.djangoproject.com/en/2.1/ref/settings/#auth-password-validators

AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]


# Internationalization， 国际化
# https://docs.djangoproject.com/en/2.1/topics/i18n/

LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'UTC'

USE_I18N = True

USE_L10N = True

USE_TZ = True


# Static files (CSS, JavaScript, Images)， 静态资源文件路径
# https://docs.djangoproject.com/en/2.1/howto/static-files/

STATIC_URL = '/static/'

STATIC_URL = '/static/'
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)

# 还可以添加 自定义配置参数
IMG_STATIC_DOMAIN = {
    'minio': "http://10.0.1.3:9000/",
    'cloudflare': "https://img.lfbbs.club/",
    'jsdelivr': "",
    'type': "minio"
}
```



自定义配置参数的使用，在需要使用的文件中

```
import os
os.environ["DJANGO_SETTINGS_MODULE"] = "app01.settings"
from django.conf import settings

def siwView(request):
    ...
    img_domain = settings.IMG_STATIC_DOMAIN["minio"]
    ...
    
```







# 三、app模块开发



**路由**

讲name之前，先说说Django template的内建标签url， {% url path.to.some_view%} 可以返回视图函数对应的URL（相对域名的绝对路径），

比如 url(r^/account/$', ‘views.index’, name=‘index’) ，

在模板中使用 {% url ‘index’ %} 将返回 /accout/ ，

这样做可以提高模版的灵活性，如果是使用硬编码的方式，模版难以维护。



- 全局路由配置 sitename/urls.py



- 各app路由配置 app01/urls.py  ，要自己创建文件

在 sitename/urls.py先导入app01的urls

```python
from django.urls import path
from django.conf.urls import include 
from app01 import urls as app01_urls

urlpatterns = [
    path('app01/', include(app01_urls))
]
```

在 app01下的urls就正常编写

- 路由规则

  ```python
  
  ```

  ** Django 1.x中有url()和path()函数，Django2. 开始使用 path()和re_path()，re_path() 是和url()一样，还要用正则语法的



### view 



- 调试方法
  
  - print('xxx')   要在终端中看打印的信息，浏览器看不到
  
    



常用模板

```python
from django.shortcuts import render
from vip import models
# Create your views here.

def recharge(request):
    if request.method == 'GET':
		number = request.GET['number']
        return render(request, 'recharge.html')

    elif request.method == 'POST':
        response = {'status': 0}
        type = request.POST['type']
        return HttpResponse(json.dumps(response))
```



获取用户信息

```python

```



获取请求数据



```python
# 平时一般提交
request.GET['username']
request.POST['username']
# 使用json格式提交
theReq = json.loads(request.POST)
theReq['username']
```



返回响应

```python 


# json格式响应
return HttpResponse(json.dumps(response))
```







数据库操作

创建和更新方式有save和objects.create、objects.update模式

用objects 模式可返回更改的数据

```python
写入保存
>>> b3 = Blog(name='Cheddar Talk', tagline='Thoughts on cheese.')
>>> b3.save()
>>> b3.id     # Returns 3.返回id

查询
>>> from django.db.models import F
>>> product = Product.objects.get(name='Venezuelan Beaver Cheese') # 返回第一条或唯一

 # 返回多条
Article.objects.filter(name="sre") 
# 匹配，like，大小写敏感，对应SQL：select * from Article where name like '%sre%'，SQL中大小写不敏感
Article.objects.filter(name__contains="sre")
# 匹配，like，大小写不敏感，对应SQL：select * from Article where name like '%sre%'，SQL中大小写不敏感
Article.objects.filter(name__icontains="sre")


# 更多请看  https://www.django.cn/course/show-18.html


更新
>>> from django.db.models import F
>>> product = Product.objects.get(name='Venezuelan Beaver Cheese')
>>> product.number_sold = F('number_sold') + 1
>>> product.save()

    #把标题'增加标题二'，修改成'我被修改了'。将指定条件的数据更新，支持 **kwargs，支持字典。
    title = models.Article.objects.filter(title='增加标题二').update(title='我被修改了')
    return HttpResponse('orm')

删除，但我的项目不建议用删除，更保守的方式是model中设计一个字段为开启可查看
title = models.Article.objects.filter(id=6).delete()

```





### templates 





# 四、models



```python
创建和变更

python3 manage.py makemigrations
python3 manage.py migrate
```



批量数据操作



批量创建

```python3
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)

product_list_to_insert = list()
for x in range(10):
    product_list_to_insert.append(Product(name='product name ' + str(x), price=x))
Product.objects.bulk_create(product_list_to_insert)
# 或者
Product.objects.bulk_create([
	Product(name = 'product name 1', price = 1),
	Product(name = 'product name 2', price = 2),
	...
])

```



### 事务





# 用户身份

参考：

https://www.django.cn/article/show-18.html

https://developer.mozilla.org/zh-CN/docs/learn/Server-side/Django/Authentication



用户模型

https://blog.csdn.net/weixin_42134789/article/details/80207322



获得当前用户信息

```python
def sample_view(request):
    current_user = request.user
    print current_user.id
    
# 
request.user.id
request.user.username
```



