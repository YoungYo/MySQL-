# 1.在合适位置创建一个目录
# 2.打开终端进入到该目录下
# 3.输入“django-admin startproject project”
# 4.目录层级说明
## 4.1 manage.py
   一个命令行工具，可以使我们用多种方式对Django项目进行交互
## 4.2 project目录
### 4.2.1 __init__.py
   这是一个空文件，它的作用是告诉Python这个目录应该被看做一个Python包
### 4.2.2 setting.py
   项目的配置文件
### 4.2.3 urls.py
   项目的url声明
### 4.4.4 wsgi.py
   项目与wsgi兼容的web服务器入口
# 5.配置数据库
  Django默认是使用SQLlite数据库，如果想使用别的数据库，需要在settings.py中通过DATABASES选项进行配置
## 5.1 配置MySQL数据库
Python3.x安装的是PyMysql，在__init__.py中写入两行代码：
```
import pymysql
pymysql.install_as_MySQLdb()
```

接下来在settings.py中配置数据库，将其中的DATABASES改成下面这样：

```SQL
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '数据库名',
        'USER': '用户名',
        'PASSWORD': '密码',
        'HOST': '数据库服务器ip',
        'PORT': '端口',
    }
}
```
# 6.创建应用

在一个项目中可以创建多个应用，每个应用处理一种业务。
在终端打开项目根目录，执行“python manage.py startapp myApp”，这样就创建了一个应用，在项目根目录下多了一个叫myApp的文件夹。

  ## 6.1 myApp目录说明
   ### 6.1.1 admin.py
站点配置

   ### 6.1.2 models.py

模型

   ### 6.1.3 view.py
视图

# 7.激活应用
在settings.py文件中，将myApp应用加入到INSTALLED_APPS中:
  ```python
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'myApp',
  ]
  ```

# 8.定义模型
模型是与数据库进行交互的，一个数据表就对应一个模型，要在models.py文件中定义模型
  ## 8.1引入models模块
因为模型类要继承models.Model类，所以要引入models模块，引入代码如下：from django.db import models（此操作是在models.py中进行）
  ## 8.2定义模型

代码如下：

```Python
from django.db import models

#Create your models here.
#下面两个类分别对应数据库中的两个表，且两个类都继承了models.Model

class Grade(models.Model):
    gname = models.ChaField(max_length = 20) #班级名称
    gdate = models.DateTimeField() #成立时间
    ggirlnum = models.IntegerField() #女生数量
    gboynum = models.IntegerField() #男生数量
    isDelete = models.BooleanField(default = False) #是否删除

class Student(models.Model):
    sname = models.CharField(max_length = 20) #姓名
    sgender = models.BooleanField(default = True) #性别
    sage = models.IntegerField() #年龄
    scontend = models.CharField(max_length = 20) #简介
    isDelete = models.BooleanField(default = False) #是否删除
    sgrade = models.ForeignKey("Grade", on_delete=models.CASCADE) #所在班级，关联外键
```
## 8.3 说明

不需要定义主键，主键在生成时会自动添加，并且值自动增加

# 9.在数据库中生成数据表

生成数据表分为两步，第一步：生成迁移文件；第二步：执行迁移

## 9.1生成迁移文件

在命令行中执行“python manage.py makemigrations myApp”，在migrations目录下生成一个迁移文件。此时数据库中还没有生成表

## 9.2执行迁移

在命令行中执行“python manage.py migrate myApp”，相当于执行SQL语句创建数据表。这时数据库中就已经存在数据表了。

# 10.测试数据操作

## 10.1执行“python manage.py shell”，进入到Python shell。
  ## 10.2引入包

```python
    from django.utils import timezone
    from datetime import *
    from myApp.models import Grade, Student
```

## 10.3查询所有数据

`类名.objects.all()` 

 比如：`Grade.objects.all()`

  ## 10.4添加数据

添加数据的本质就是创建一个模型类的实例。
示例代码如下：



​    grade01 = Grade()
​    grade01.gname = "Python"
​    grade01.gdate = datetime(year=2018, month=9, day=13)
​    grade01.ggirlnum = 3
​    grade01.gboynum = 70
​    grade01.save() #最后要保存，这一步执行完以后，数据库中的Grade表中就多了一条记录

  ## 10.5查看某个数据

​    查询ID等于2的记录：Grade.objects.get(pk = 2)
​    或者：g = Grade.objects.get(pk = 2)，这样就是将查询的结果赋值给g了，可以通过g对记录做修改

##  10.6修改数据

​    g = Grade.objects.get(pk = 2) #获取记录
​    g.gname = 'C++' #将此条记录的gname的值改为“C++”
​    g.save() #保存

##  10.7删除数据

​    g = Grade.objects.get(pk = 2) #获取记录
​    g.delete() #将此条记录删除
​    g.save() #保存。此时数据库中就不存在这条记录了

##  10.8关联对象

​    创建一个学生对象，他的所属班级是id为1的班级。
​    grade01 = Grade.objects.get(pk = 1) #获取id为1的班级对象
​    stu = Student()
​    stu.sname = 'young'
​    stu.sgender = True
​    stu.sage = 20
​    stu.scontend = "My name is young"
​    stu.sgrade = grade01 #这一步就是指定该学生的所属班级是grade01
​    stu.save()
​    还有一种方式：
​    stu02 = grade01.student_set.create(sname = 'Tom', sgender = True, sage = 22, scontend = 'My name is Tom') #这种方式不用保存，直接存到数据库中了

  ## 10.9获取关联对象的集合

​    例：获取id为1的班级的所有学生，代码如下：

```python
grade01 = Grade.objects.get(pk = 1)
grade01.student_set.all()
```


获取关联对象的集合的代码模板为：`对象名.关联的类名_set.all()`

# 11.启动服务器

  在命令行中执行：`python manage.py runserver ip:port`
  `ip:port`可以省略，如果省略，则默认是本机ip，端口号默认是8000
  ***注：这是一个纯Python写的轻量级web服务器，仅仅在开发测试时使用***

# 12.admin站点管理

##  12.1 概述

admin站点管理的作用是负责内容发布（包括负责添加、修改、删除内容）和公告访问

##  12.2 配置admin应用

在settings.py文件中的INSTALLED_APPS中添加`django.contrib.admin`。（一般情况下已经存在）

 ## 12.3 创建管理员用户

打开命令行，进入项目根目录，执行：`python manage.py createsuperuser`
然后会提示输入用户名，如果不输入任何字符，直接回车，那么默认用户名就是administrator，然后会提示输入邮箱，然后输入密码。

我这里直接默认用户名是administrator，密码是123456
在浏览器中输入http://127.0.0.1:8000/admin （这是在本机测试时的网址，具体网址根据情况而定）进入管理页面。

## 12.4 汉化

把settings.py中的LANGUAGE_CODE的值改为`zh-Hans`，把TIME_ZONE的值改为`Asia/Shanghai`，刷线浏览器页面，就完成了汉化。    

## 12.5 管理数据表

### 12.5.1 修改settings.py文件

在admin.py中注册模型，也就是数据表。代码如下：

```python
from django.contrib import admin

# Register your models here.

from .models import Grade, Student #从models.py中引入Grade类和Student类

#注册
admin.site.register(Grade) #注册班级类
admin.site.register(Student) #注册学生类
```

注册完成后，保存settings.py，刷新管理页面。可以看到页面上多了我们添加的两个类。

![](D:\Study\GitHub\PythonWeb-note\image\2018-09-14_114410.png)

### 12.5.2 自定义管理页面


















