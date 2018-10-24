#**实验二**<br>
第1步：以system登录到pdborcl，创建角色con_res1_view和用户new_wang9527，并授权和分配空间：<br>
$ sqlplus system/123@pdborcl<br>
SQL> CREATE ROLE con_res1_view;<br>
Role created.<br>
SQL> GRANT connect,resource,CREATE VIEW TO con_res1_view;<br>
Grant succeeded.<br>
SQL> CREATE USER new_wang9527 IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;<br>
User created.<br>
SQL> ALTER USER new_wang9527 QUOTA 50M ON users;<br>
User altered.<br>
SQL> GRANT con_res1_view TO new_wang9527;<br>
Grant succeeded.<br>
SQL> exit<br><br><br>


第2步：新用户new_user连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。<br>
$ sqlplus new_wang9527/123@pdborcl<br>
SQL> show user;<br>
USER is "NEW_USER"
SQL> CREATE TABLE mytable (id number,name varchar(50));<br>
Table created.<br>
SQL> INSERT INTO mytable(id,name)VALUES(1,'wang');<br>
1 row created.<br>
SQL> INSERT INTO mytable(id,name)VALUES (2,'peng');<br>
1 row created.<br>
SQL> CREATE VIEW myview AS SELECT name FROM mytable;<br>
View created.<br>
SQL> SELECT * FROM myview;<br>
NAME<br>
--------------------------------------------------<br>
wang<br>
peng<br>
SQL> GRANT SELECT ON myview TO hr;<br>
Grant succeeded.<br>
SQL>exit<br>
第3步：用户hr连接到pdborcl，查询new_user授予它的视图myview<br>
$ sqlplus hr/123@pdborcl<br>
SQL> SELECT * FROM new_user.myview;<br>
NAME<br>
--------------------------------------------------<br>
wang<br>
peng<br>
SQL> exit<br>
#**查看数据库的使用情况**<br>
以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。<br>

$ sqlplus system/123@pdborcl<br>

SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';<br><br>

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",<br>
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",<br>
 Round(( total - free )/ total,4)* 100 "使用率%"<br>
 from (SELECT tablespace_name,Sum(bytes)free<br>
        FROM   dba_free_space group  BY tablespace_name)a,<br>
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files<br>
        group  BY tablespace_name)b<br>
 where  a.tablespace_name = b.tablespace_name;<br>
 $[image]()
 SQL-DEVELOPER修改用户的操作界面：<br>
 $[image](https://github.com/wangmingran9527/oracle/blob/master/test2/3.PNG)<br>
 sqldeveloper授权对象的操作界面：<br>
 $[image](https://github.com/wangmingran9527/oracle/blob/master/test2/5.PNG)<br>
