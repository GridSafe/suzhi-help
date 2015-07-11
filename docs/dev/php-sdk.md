# PHP SDK

[GitHub 地址](https://github.com/GridSafe/grid-sdk-php)


## 使用说明

- 下载文件，解压后，进入 `php/src` 目录，修改 `config.inc.php` 文件，定义如下2个常量的值，写入您自己的 `email` 账号与 `api signature`：
- `define('CDNZZ_USER', 'root@cdnzz.com')`;
- `define('CDNZZ_SIGNATURE', '5435ac7f17ddfdb759ae50d2e3b17667')`;
- 其中,您的 api signature 值可以在 [个人首页]() 查询
- 更多信息请参考 [用户 API 文档](api-docs.md) 与 [错误代码列表](error-code.md)


## 样例

```php
require_once "./cdnzzv1.sdk.class.php";
try{
    $cdnzz = new CDNZZ();
    $url = "https://***.com/index.php?a=asd&b=mid";

    //清缓存
    $response = $cdnzz->purge_cache($url);
    print_r($response);

    //预加载
    $response = $cdnzz->preload($url);
    print_r($response);
}catch(Exception $e){
    echo $e->getMessage();
}
```

## 版本历史
 - v0.1.0:(2013-09-03): 实现了清缓存、预加载的调用

