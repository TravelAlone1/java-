[TOC]

# 不建议在MySql 中使用UTF-8?

最近我遇到了一个bug，我试着通过Rails在以“utf8”编码的MariaDB中保存一个UTF-8字符串，然后出现了一个离奇的错误：



```mysql
Incorrect string value: ‘ð <…’ for column ‘summary’ at row 1
```



我用的是UTF-8编码的客户端，服务器也是UTF-8编码的，数据库也是，就连要保存的这个字符串“😃”也是合法的UTF-8。

MySql的 "utf8" 实际上不是真正的UTF-8。

“utf8”只支持每个字符最多三个字节，而真正的UTF-8是每个字符最多四个字节。

MySQL一直没有修复这个bug，他们在2010年发布了一个叫作“utf8mb4”的字符集，绕过了这个问题。

当然，他们并没有对新的字符集广而告之（可能是因为这个bug让他们觉得很尴尬），以致于现在网络上仍然在建议开发者使用“utf8”，但这些建议都是错误的。

简单概括如下：

1.MySQL的“utf8mb4”是真正的“UTF-8”。

2.MySQL的“utf8”是一种“专属的编码”，它能够编码的Unicode字符并不多。

我要在这里澄清一下：所有在使用“utf8”的MySQL和MariaDB用户都应该改用“utf8mb4”，永远都不要再使用“utf8”。

