# Go SDK

[GitHub](https://github.com/GridSafe/grid-sdk-golang)


## 使用说明
- 下载文件解压后，修改`grid-sdk-golang/src/config/config.go`文件的`UserName和UserSignature`。
- 其中,您的 `api signature` 值可以在 [个人首页]() 查询
- 更多信息请参考 [用户 API 文档](api-docs.md) 与 [错误代码列表](error-code.md)


## 样例
```
import "./grid-sdk-golang/src/sdk"

func main() {
    // 预加载
    result, msg := grid_sdk.Preload("http://www.example.com/img.js")
    fmt.Println(result)
    fmt.Println(msg)

    // 清缓存
    result, msg := grid_sdk.PurgeCache("http://www.example.com/img.js")
    fmt.Println(result)
    fmt.Println(msg)
}
```

## 版本历史
 - v0.1.0:(2013-09-11): 实现了清缓存、预加载的调用

