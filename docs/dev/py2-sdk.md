# Python2 SDK

[github地址](https://github.com/GridSafe/grid-sdk-python2)


## 使用说明

- 下载文件解压后，修改`python2/src/config.py` 文件的 `USER_NAME` 和 `USER_SIGNATURE`
- 其中,您的 `api signature` 值可以在 [个人首页]() 查询
- 更多信息请参考 [用户 API 文档](api-docs.md) 与 [错误代码列表](error-code.md)

## 样例
```
# -*- coding: utf-8 -*-

from grid_sdk import GridSdk

if __name__ == "__main__":

    example = GridSdk()
    src_url = "http://www.cdnzz.com/d/a.apk"

    # 预加载
    example.preload(src_url)

    # 清缓存
    example.purge_cache(src_url)
```

## 版本历史
 - v0.1.0:(2013-09-03): 实现了清缓存、预加载的调用
