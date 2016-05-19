## 安全链接

对配置了安全链接的路径下的链接，需要通过KEY和过期时间对参数加密生成token作为参数。

example: 

+ /s/link?szt=NMfWQ9ibrdg05VEQipuDYQ&sze=2147483647
+ 上面的链接中，szt表示token，sze表示过期时间

### token生成方法

参与验证的参数：

参数|说明
------|------
sze|过期时间
uri|链接路径
domain|域名
remote_address|用户IP地址
key|KEY值

生成算法： 
 
* 将以上参数的值依次拼接成字符串`base_str = $expire$uri$domain$remote_address$key`
* 对`base_str`用md5加密，生成二进制md5加密串`bin_str`
* 对`bin_str`用base64加密，生成`bs64_str`
* 将`bs64_str`中的`=`删除，`+`替换成`-`，`/`替换成`_`

example：  

* shell

```
input:
expire="2147483647"
uri="/s/link"
domain="127.0.0.1"
remote_address="127.0.0.1"
key=" secret"

echo -n $expire$uri$domain$remote_address$key | \
openssl md5 -binary | openssl base64 | tr +/ -_ | tr -d =


output:
NMfWQ9ibrdg05VEQipuDYQ
```

* python

```python
import md5
import base64


def get_token(expire, uri, domain, remote_address, key):
    base_str = "%s%s%s%s%s" % (expire, uri, domain, remote_address, key)
    bin_str = md5.md5(base_str).digest()
    bs64_str = base64.b64encode(bin_str)
    token = bs64_str.replace("+", "-").replace("/", "_").replace("=", "")
    return token
```

### 路径匹配

路径格式|匹配说明
-------|-------
/abc*|检测以 /abc 开头的所有请求url地址
*.pdf|检测以 .pdf 结尾的所有请求url地址
/abc*.pdf|检测以.pdf结尾，以/abc开头的所有请求url地址