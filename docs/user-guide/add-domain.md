## 添加域名


### 添加域名

注册成功后，您可以自由添加或删除域名。若您还未注册我们帐号，请点击 [注册链接](https://www.cdnzz.com/personal/sign-up) 进行注册

首先需要登录我们的控制中心

![](../static/img/user-guide/add-domain-1.jpg)

在控制中心的 云分发 处点击管理域名，然后点击添加域名
![](../static/img/user-guide/add-domain-2.jpg)

![](../static/img/user-guide/add-domain-3.jpg)

### 验证域名

我们需要验证域名是否属于你，目前使用的是 CNAME 的验证方法

![](../static/img/user-guide/add-domain-4.jpg)

### 修改 DNS 记录

在域名“解析设置”处新增一条 TXT 记录，其它常见域名托管商的也是类似的

添加成功后 TXT 记录类似下图
![](../static/img/user-guide/add-domain-5.jpg)


### 确认域名验证

之后点击下一步就可以了，如果验证失败，请通过 `nslookup` `dig` 等命令去检查是否有相应的记录。

也可以通过 [在线工具](http://tool.chinaz.com/nslookup) 去检查。

如果确认记录已经正确，但还是不能验证成功，请联系我们。
