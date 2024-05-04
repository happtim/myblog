
服务器是Windows，使用软件备份数据表名都是小写的。开发环境使用mysql的docker环境，需要测试数据库时发生错误。

查阅文档发现只有在创建容器时指定 lower_case_table_names=1 才可以生效。

![image.png](https://assets.happtim.com/image/n3dc/202404231343294.png)

在大多数情况下，这需要在第一次启动MySQL服务器之前在MySQL选项文件中配置lower_case_table_names。

故修改my.cnf 无效。

需要在第一次启动mysql容器时指定。

```
docker run --name mysql --restart=always \
    -v /home/mysql/conf/my.cnf:/etc/mysql/my.cnf \
    -v /home/mysql/data2:/var/lib/mysql \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD="123456" \
    -e TZ=Asia/Shanghai \
    -d mysql:8.0 --lower-case-table-names=1
```

lower-case-table-names 参数解释：

- 0：表名和数据库名区分大小写，同时在比较时也区分大小写。
- 1：表名和数据库名不区分大小写，但在比较时区分大小写。
- 2：表名和数据库名不区分大小写，并且在比较时也不区分大小写。


