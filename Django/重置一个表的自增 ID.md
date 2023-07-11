1. 首先打开 Django shell:

```python
python manage.py shell
```
![[Pasted image 20230629000637.png]]
然后复制这段代码进行修改

```python
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("DELETE FROM sqlite_sequence WHERE name='<具体表名称>';")

```
如果表中还有记录，那么重置自增 ID 可能并不会达到你想要的效果，因为自增 ID 的值不会影响已有的记录，新插入的记录的 ID 仍然会递增，直至超过已存在的最大 ID 值。


原理下面解析：

1. 在 SQLite 中，`sqlite_sequence` 表记录了每个表的自增 ID 的最新值。你可以通过修改 `sqlite_sequence` 表来重置自增 ID。
2. 要重置一个表的自增 ID，可以删除 `sqlite_sequence` 表中该表的记录。在 Django 中，可以使用如下 SQL 语句来实现：
```python
cursor.execute("DELETE FROM sqlite_sequence WHERE name='your_table_name';")
```
3. 删除操作之后，可以查询 `sqlite_sequence` 表来确认操作是否成功：
```python
    cursor.execute("SELECT * FROM sqlite_sequence WHERE name='your_table_name';") 
    rows = cursor.fetchall()
     print(rows)  # 如果 rows 是空的，那就表示 DELETE 操作成功了
```
4. 重置自增 ID 只有在表中没有任何记录的情况下才有效。如果表中还有记录，那么新插入的记录的 ID 将会是已存在的最大 ID 加一，无论 `sqlite_sequence` 表中的值是多少。


