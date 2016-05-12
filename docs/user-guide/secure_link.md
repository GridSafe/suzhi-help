## 安全链接

对配置了安全链接的路径下的链接，需要通过KEY和过期时间对参数加密生成token作为参数。


### token生成方法

参与验证的参数：

参数|说明
------|------
expire|过期时间
uri|链接路径
domain|域名
key|KEY值

生成算法： 
 
* 将以上参数的值依次拼接成字符串base_str = $expire$uri$domain$key
* 对base_str用md5加密，生成二进制md5加密串bin_str
* 对bin_str用base64加密，生成bs64_str
* 将bs64_str中的`=`删除，`+`替换成`-`，`/`替换成`_`

example：  

* shell

```
input:
expire="2147483647"
uri="/s/link"
domain="127.0.0.1"
key=" secret"

echo -n $expire$uri$domain$key | \
openssl md5 -binary | openssl base64 | tr +/ -_ | tr -d =


output:
_e4Nc3iduzkWRm01TBBNYw
```

* python

```python
import md5
import base64


def get_token(expire, uri, domain, key):
    base_str = "%s%s%s%s" % (expire, uri, domain, key)
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