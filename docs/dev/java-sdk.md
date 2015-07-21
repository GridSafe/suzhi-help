## Java SDK

[GitHub 地址](https://github.com/GridSafe/grid-sdk-java)


### 使用说明

- 下载文件解压后，按照下面的例子调用即可。调用时要传入你的email地址与api signature值
- 其中,您的 api signature 值可以在 [个人首页]() 查询
- 更多信息请参考 [用户 API 文档](api-docs.md) 与 [错误代码列表](error-code.md)

### 样例

```
public class example{
    public static void main(String[] args) {
        //传入email与api signature
        CDNZZ cdn = new CDNZZ("lixiang@aaa.com", "5424ac7f17ddfdb759ae50d2e3b17667");
        //清缓存
        String ret = cdn.purgeCache("http://www.cdnzz.com/api/down.apk");
        System.out.println(ret);
        //预加载
        ret = cdn.preload("http://www.cdnzz.com/api/down.apk");
        System.out.println(ret);
    }
}
```

### 版本历史
 - v0.1.0:(2013-09-03): 实现了清缓存、预加载的调用
