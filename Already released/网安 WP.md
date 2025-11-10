#  校级网络安全竞赛“新生赛道” WP

# Crypto

## RSA2

使用[factor.db](http://factordb.com/)分解质因数 n

![image](https://www.gwy.fun/blog_ima/blog/image-20251109162850791.png)

编写 Py 脚本

![image](https://www.gwy.fun/blog_ima/blog/image-20251109162928921.png)



## 少见的base

通过尝试发现为Base58编码 解密

![image](https://www.gwy.fun/blog_ima/blog/image-20251109163118981.png)

## 签到题

将最后一个字符更改为 }

# Misc

## fakezip

一开始打开压缩包提示输入密码 但其实是伪加密

故用 010 Editor 打开压缩包 删除 zip 加密标志

需要进入General purpose bit flag

将中心目录和本地头同时修改 以免解压报错

正常解压后有一张图片便是 flag

![image](https://www.gwy.fun/blog_ima/blog/image-20251109163328182.png)

![image](https://www.gwy.fun/blog_ima/blog/image-20251109163818471.png)

# Reverse

## lucknum

使用 64 位 IDA打开文件 进行反编译 在 main 中发现 flag

![449b86a176aeee80c214b8c9102c550e](https://www.gwy.fun/blog_ima/blog/449b86a176aeee80c214b8c9102c550e.jpg)

# web

## weak_auth

弱密码爆破 

爆破前尝试最常见的 admin/123456 成功拿到 flag

## Easy_web

通过尝试用户名User does not exist 测试到管理员用户名为 admin

尝试爆破无果后注册账号

发现需要提权 尝试 changePwd 接口无果

后发现忘记密码修改接口可能存在漏洞 使用 Burp Suite 修改请求

![image](https://www.gwy.fun/blog_ima/blog/image-20251109165654719.png)

后成功登录管理账号 点击 Manage 提示IP Not allowed

加入头X-Forwarded-For:127.0.0.1

![image](https://www.gwy.fun/blog_ima/blog/image-20251109165828268.png)

![image](https://www.gwy.fun/blog_ima/blog/image-20251109165843229.png)

进入源码后发现注释 指向文件管理 推测上传点

![image](https://www.gwy.fun/blog_ima/blog/image-20251109165949336.png)

需要伪装图片格式文件注入 记事本创建文件`<?php phpinfo(); ?>`更改后缀名为 jpg 

尝试探测信息 看能不能调取环境变量等

使用Burp Suite 拦截发送到 Repeater更改 filename 为 php4

后失败 换为`<script language="php"> phpinfo()</script>`成功获取 flag

![image](https://www.gwy.fun/blog_ima/blog/image-20251109170652281.png)

## baby_python

![image](https://www.gwy.fun/blog_ima/blog/image-20251109171344391.png)

第一次尝试确认可以执行表达式 大概率是 Jinja2

测试常见的方式 {{config}}   {{self}} {{[].class}}会被拦截

故需要转换 将[b] 改为 .getitem(b)

![image](https://www.gwy.fun/blog_ima/blog/image-20251109171504121.png)



尝试使用object.**subclasses**() 列举所有类

点属性链可能被拦截 额外过滤了 base 或 pop  

改为getattr链 打包成普通函数和字符串参数

但不同环境中 subclasses 的顺序不同 试一下

**获得起点`{{getattr(getattr(getattr(0,'__class__'),'__mro__'),'__getitem__')(1)}}`**

**得到长度`{{getattr(getattr(getattr(0,'__class__'),'__mro__'),'__getitem__')(1).__subclasses__().__len__()}}`** 

为 225

**挨个遍历`{{getattr(getattr(getattr(0,'__class__'),'__mro__'),'__getitem__')(1).__subclasses__().__getitem__(40).__name__}}`**



![image](https://www.gwy.fun/blog_ima/blog/image-20251109175433711.png)



**得到 flag`{{getattr(getattr(getattr(0,'__class__'),'__mro__'),'__getitem__')(1).__subclasses__().__getitem__(40)('/flag').read()}}`**



