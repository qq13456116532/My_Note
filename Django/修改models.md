
当你修改了models的字段之后，需要执行如下的命令

```python
python manage.py makemigrations
python manage.py migrate
```
在某些情况下，数据库的状态可能没有正确地同步到 Django。你可以尝试运行 

```python
python manage.py syncdb
```

来同步数据库状态。