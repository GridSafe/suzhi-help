# Nodejs SDK

[GitHub](https://github.com/GridSafe/grid-sdk-nodejs)

## 使用说明

- 下载文件解压后，修改 `nodejs/src/config.js` 文件的 `USER_NAME` 和 `USER_SIGNATURE`。
- 其中,您的 `api signature` 值可以在 [个人首页]() 查询
- 更多信息请参考 [用户 API 文档](api-docs.md) 与 [错误代码列表](error-code.md)


## 样例
```
var sdk = require('./grid_sdk')

/*清缓存*/
sdk.purgeCache("http://www.***.com/public/js/jquery.min.js")

/*预加载*/
sdk.preload("http://www.***.com/public/js/jquery.min.js")

```
## 版本历史
 - v0.1.0:(2013-09-03): 实现了清缓存、预加载的调用

