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

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-14_114410.png)

### 12.5.2 自定义管理页面

假设需要管理班级类，那么要在admin.py中定义一个类：GradeAdmin，这个类要继承admin.ModelAdmin，代码如下：

```python
from django.contrib import admin

# Register your models here.

from .models import Grade, Student

#注册
class GradeAdmin(admin.ModelAdmin):
    #列表页属性

    list_display = ['pk', 'gname', 'gdate', 'ggirlnum', 'gboynum', 'isDelete']
    list_filter = ['gname']
    search_fields = ['gname']
    list_per_page = 5

    #添加、修改页属性，fields与fieldsets不能同时使用
    #fields = ['gname', 'gdate', 'isDelete', 'gboynum', 'ggirlnum']
    fieldsets = [
        ('num', {'fields':['ggirlnum', 'gboynum']}),
        ('base', {'fields':['gname', 'gdate', 'isDelete']})
    ]

admin.site.register(Grade, GradeAdmin)

admin.site.register(Student)
```

#### 属性说明

`list_display`：显示字段

`list_filter`：过滤字段

`search_fields`：搜索字段

`list_per_page`：分页

`fields`：添加记录或修改属性时，属性显示的先后顺序

`fieldset`：给属性分组

分组后的效果如下图所示：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-18_233436.png)

**注意：`fields`与`fieldset`不能同时食用**

#### 关联对象

如何在创建一个班级时，可以直接添加若干个学生？

在admin.py中定义一个类，继承`admin.TabularInline`类或`admin.StackedInline`类。然后在`GradeAdmin`类中加一行代码：`inlines = [StudentInfo]`，至此，admin.py的全部代码如下：

```python
from django.contrib import admin

# Register your models here.

from .models import Grade, Student

#注册
class StudentInfo(admin.TabularInline):
    model = Student
    extra = 2 #extra表示你在添加班级时，能够添加几个学生
class GradeAdmin(admin.ModelAdmin):
    inlines = [StudentInfo]
    #列表页属性
    list_display = ['pk', 'gname', 'gdate', 'ggirlnum', 'gboynum', 'isDelete']
    list_filter = ['gname']
    search_fields = ['gname']
    list_per_page = 5

    #添加、修改页属性
    #fields = ['gname', 'gdate', 'isDelete', 'gboynum', 'ggirlnum']
    fieldsets = [
        ('num', {'fields':['ggirlnum', 'gboynum']}),
        ('base', {'fields':['gname', 'gdate', 'isDelete']})
    ]

admin.site.register(Grade, GradeAdmin)

#注册
class StudentAdmin(admin.ModelAdmin):
    list_display = ['pk', 'sname', 'sgender', 'sage', 'scontend', 'isDelete', 'sgrade_id']
    list_per_page = 2

admin.site.register(Student, StudentAdmin)
```

`StudentInfo`继承`admin.TabularInline`类时的效果图：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_000900.png)

`StudentInfo`继承`admin.StackedInline`类时的效果图：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_101435.png)

#### 布尔值显示问题

原始状态下，学生性别的显示是下面红框中的那样，因为我们在创建数据表的时候，sgender这个字段是bool类型的，如果是男生，值就是true，如果是女生，值就是false。如果我们想让它显示男女怎么办呢？

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_103245.png)

我们需要在admin.py文件中的`StudentAdmin`类中定义一个方法，方法名任意，这里取名为`displayGender`，方法体代码如下：

```python
def displayGender(self):
	if self.sgender:
		return "男"
    else:
        return "女"
```

然后在`StudentAdmin`类的`list_display`这个属性中把`'sgender'`改为方法名，方法名不需要加引号。完整代码如下：

```python
class StudentAdmin(admin.ModelAdmin):
    def displayGender(self):
        if self.sgender:
            return "男"
        else:
            return "女"
    list_display = ['pk', 'sname', displayGender, 'sage', 'scontend', 'isDelete', 'sgrade_id']
    list_per_page = 2
```

保存文件，刷新页面，效果图如下：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_104713.png)

同样，其他的布尔值的显示也可以通过上述方法修改。

#### 更改字段名的显示方式

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_104713_modified.png)

如上图所示，在显示数据时，字段名的显示是按照数据库中的格式显示的，如果我们想让它按照我们设想的方式显示应该怎么办呢？比如，在姓名那一栏，不让他显示“SNAME”，而是显示“姓名”。

首先，跟前面一样，在admin.py文件中的`StudentAdmin`类中先定义一个方法，方法体如下：

```python
def displayName(self):
	return self.sname
```

然后在`StudentAdmin`类的`list_display`这个属性中把`'sname'`改为方法名，方法名不需要加引号。最后再添加一行代码：`displayName.short_description = "姓名"`

同样，如果我们想改性别那一栏，操作方法也是如此。之前我们为了改变布尔值的显示方式，定义了一个方法叫做`displayGender`，然后刷新浏览器后我们发现性别那一栏就显示“DISPLAYGENDER”了，而不是显示原来的“SGENDER”。如果我们想让它显示“性别”，因为我们已经定义过了方法，所以现在直接添加一行代码就可以：`displayGender.short_description = "性别"`

完整代码如下：

```python
class StudentAdmin(admin.ModelAdmin):
    def displayGender(self):
        if self.sgender:
            return "男"
        else:
            return "女"
    def displayName(self):
        return self.sname
    list_display = ['pk', displayName, displayGender, 'sage', 'scontend', 'isDelete', 'sgrade_id']
    list_per_page = 2
    displayGender.short_description = "性别"
    displayName.short_description = "姓名"
```

修改后显示效果如下：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_111243.png)

同样，要想修改其他字段，步骤也是一样。

#### 执行动作位置问题

下图中红框圈出来的就是执行动作的部分，如果想把它放到底部，操作如下：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_111828.png)

在admin.py文件中的`StudentAdmin`类中添加如下代码：

```python
actions_on_top = False
actions_on_bottom = True
```

刷新浏览器页面，效果如下：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-19_113042.png)

如果想让它既在上面又在下面，那么就让`actions_on_top`和`actions_on_bottom`等于`True`，即：

```python
actions_on_top = True
actions_on_bottom = True
```

### 12.5.3 使用装饰器完成注册

定义`StudentAdmin`类来管理学生类`Student`的时候，需要进行注册，之前我们使用的是这行代码：`admin.site.register(Student, StudentAdmin)`（可在前面的代码中找到），以后注册的时候不这样做了，我们用装饰器进行注册，就是在类`StudentAdmin`前面加上：`@admin.register(Student)`。同样，在管理别的类的时候，也是这样进行注册，括号里面的就是你要管理的类名。

# 13. 视图的基本使用

## 13.1 概述

在Django中，视图对web请求进行回应。视图就是一个Python函数，在view.py文件中定义。

## 13.2 定义视图

先定义一个简单的视图：

```python
from django.shortcuts import render

# Create your views here.

from django.http import HTTPResponse

#请求器
def index(request):
    return HTTPResponse("Hello world!")
```

## 13.3 配置URL

在myApp目录下创建一个urls.py文件，里面代码如下：

```Python
from django.conf.urls import url
from . import views
urlpatterns = [
    url(r'^$', views.index)
]
```

修改project目录下的urls.py文件：

```python
from django.conf.urls import url, include
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myApp.urls')) #仅输入IP地址，后面不带任何参数时，跳转到这个myApp.urls里面的index
]
```

接下来在浏览器中输入`http://127.0.0.1:8000`：

![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-21_130502.png)

可以看到，页面上打印出了“Hello world!”

# 14. 模板的基本使用

## 14.1 概述

模板是HTML页面，可以根据视图中传递过来的数据进行填充

## 14.2 创建模板目录

在项目根目录（即project目录）下创建目录templates，也就是说templates与myApp目录是同级的。然后在templates目录下创建一个myApp目录，这个目录用来存放应用myApp的模板。

## 14.3 配置模板路径

修改settings.py文件中的TEMPLATES的DIRS的值：`'DIRS': [os.path.join(BASE_DIR, 'templates')]`

## 14.4 定义模板

在templates/myApp目录下创建两个HTML文件，grade.html和student.html

### 14.4.1 模板语法

1. {{输出值，可以是变量，也可以是对象.属性}}
2. {%执行代码段%}

### 14.4.2 实现一个功能

当我在浏览器中输入`http://127.0.0.0:8000/grade` 的时候，浏览器上显示班级信息。

具体操作步骤如下：

1. 修改project/myApp/urls.py，在	`urlpattern `中添加一个元素：`url(r'grade/$', views.grade)`，改完之后文件内容如下：

   ```python
   from django.conf.urls import url
   from . import views
   urlpatterns = [
       url(r'^$', views.index),
       url(r'grade/$', views.grade)
   ```

2. 修改project/myApp/views.py，在文件中添加如下代码：

   ```python
   from .models import Grade, Student
   def grade(request):
       #去模板中取数据
       gradeList = Grade.objects.all()
       #将数据传递给模板，模板再渲染页面，将渲染好的页面返回给浏览器
       return render(request, 'myApp/grade.html', {"grade":gradeList})
   ```

3. 修改project/templates/myApp/grade.html，文件代码如下：

   ```html
   <!DOCTYPE html>
   <html lang = "en">
   <head>
   	<meta charset="UTF-8">
   	<title>班级信息</title>
   </head>
   <body>
   	<h1>班级信息列表</h1>
   	<ul>
   		{%for g in grade%}
   		<li>
   			<a href="#">{{g.gname}}</a>
   		</li>
   		{%endfor%}
   	</ul>
   </body>
   </html>
   
   ```

4. 在浏览器中输入网址，效果如下：

   ![](https://raw.githubusercontent.com/YoungYo/PythonWeb-note/master/image/2018-09-21_141130.png)





















