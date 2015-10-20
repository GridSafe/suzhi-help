## DNSPOD CNAME 接入云加速

1. 登入dnspod的域名管理页面

2. 点击【我的域名】>>点击需要解析的域名，如图
![](../static/img/domain-access/dnspod-1.jpg)


3. 在解析中，记录类型选择【CNAME】>>主机记录填写 **子域名名称** >> 记录值填写云加速 **分配CNAME别名**。注：不同的子域名云加速分配的CNAME别名是不同的【同一个子域名若已经存在A记录，请删除A记录以免发生冲突】
![](../static/img/domain-access/dnspod-2.jpg)
