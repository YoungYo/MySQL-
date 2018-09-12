1.在合适位置创建一个目录
2.打开终端进入到该目录下
3.输入“django-admin startproject project”
4.目录层级说明
  4.1 manage.py
    一个命令行工具，可以使我们用多种方式对Django项目进行交互
  4.2 project目录
    4.2.1 __init__.py
      这是一个空文件，它的作用是告诉Python这个目录应该被看做一个Python包
    4.2.2 setting.py
      项目的配置文件
    4.2.3 urls.py
      项目的url声明
    4.4.4 wsgi.py
      项目与wsgi兼容的web服务器入口
5.配置数据库
  Django默认是使用SQLlite数据库，如果想使用别的数据库，需要在settings.py中通过DATABASES选项进行配置
  5.1 配置MySQL数据库
    Python3.x安装的是PyMysql，在__init__.py中写入两行代码
    import pymysql
    pymysql.install_as_MySQLdb()
    接下来在settings.py中配置数据库，将其中的DATABASES改成下面这样：
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
