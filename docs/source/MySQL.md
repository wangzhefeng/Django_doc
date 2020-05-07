
# MySQL

## 1.启动 MySQL

```shell
mysql -u root -p
123456
```

## 2.创建 `mysite_db` 数据库

```shell
create database mysite_db default charset=utf8mb4 default collate utf8mb4_unicode_ci;
show databases;
```

## 3.创建 `wangzf` 用户并授权

```shell
# 登录 wangzf 用户
create user 'wangzf'@'localhost' identified by '123456';
grant all privileges on mysite_db.* to 'wangzf'@'localhost';
flush privileges;

# 登录 wangzf 用户
mysql -u wangzf -p
123456
show databases;
```

## 4.在 `mysite` 项目中配置 MySQL

编辑 `./mysite/mysite/settings.py`:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite_db',
        'USER': 'wangzf',
        'PASSWORD': '123456',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

## 5.初始化数据库

```shell
cd mysite
pip3 install mysqlclient
pip3 install pymysql
```

编辑 `./mysite/mysite/__init__.py`:

```python
import pymysql
pymysql.install_as_MySQLdb()
```

```python
python3 manage.py migrate
```

```
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying blog.0001_initial... OK
  Applying sessions.0001_initial... OK
```

## 6.若有缓存表，也需要创建

```shell
cd mysite
python3 manage.py createcachetable
```

## 7.迁移数据

使用 Django 导出导入数据的命令完成迁移 `SQLite -> MySQL`：

```python
# 修改 settings.py 配置文件中的数据库为 SQLite
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

```shell
cd mysite
python3 manage.py dumpdata > data.json
```

将 `data.json` 导入 MySQL：

```python
# 修改 settings.py 配置文件中的数据库为 SQLite
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mysite_db',
        'USER': 'wangzf',
        'PASSWORD': '123456',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

```shell
mysql -u wangzf -p
123456
use mysite_db;
show tables;
SELECT * FROM django_content_type;
DELETE FROM auth_permission;
DELETE FROM django_content_type;
```

```shell
cd mysite
python3 manage.py loaddata data.json
```

## 8.MySQL 时区问题

日期归档文章的数量显示不正确，MySQL 没有加载时区相关的表：

```shell
mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root -p 123456 mysql
```

## 9.启动本地 Django 服务

```shell
python3 manage.py runserver
```

