# 速致直播 API 文档
------------

* 更新时间 1: 2015/11/10

## 基本说明

API 请求地址 - `https://www.cdnzz.com/apiv3/json`

基于 HTTPS 使用 POST 方式传递参数。

具体的 API 验证方法:

  1. 首先需要通过速致 API 获取相应的 token（ [参考在线文档](https://docs.cdnzz.com/dev/api-v3/#token) ）
  2. 通过获取到的 token 进行视频转码的 API 请求

注意，通过速致 API 获取到的 token 可以设置过期时间，过期时间内可以通过 token 去调用别的相关 API。

## RTMP 直播配置

### 添加 RTMP

- **说明**: 添加一个指定时间的 RTMP 直播配置
- **参数**:
    |   参数   |    必选   |   类型    |   说明  |  
    |   :----: |   :-----: |   :-----: |   :------: |  
    |   beg    |    True   |    string |   开始时间，格式为 `%Y%m%d%H%M%S` 如，20151011010101 |  
    |   end    |    True   |    string |   结束时间，格式为 `%Y%m%d%H%M%S` - record: 是否进行录制，默认为 0，表示不录制，1 表示录制 |  
    |   file_path |    True |    string |   录制的指定路径 |  
- **注意**:
    - 只有企业用户才可以进行直播的配置
    - 使用直播录制的时候，需登录我们官网 首页 -> 产品 -> 云直播 处配置存储
    - 当 record 为 0 时，或者没有配置好存储，file_path 无效
- **示例**:
`user=apitest@cdnzz.com&token=<token>&method=AddRTMP&beg=20150101010101&end=20150102010101&record=1&file_path=/abc`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
      "id": 1
      "beg": '20150101010101', # 格式为 %Y%m%d%H%M%S
      "end": '20150102010101',
      "record": 1,
      "file_path": '/abc',
      "push_url": ...,
      "pull_url": ...
    }
}
```

### 获取所有的 RTMP 信息

- **说明**: 获取所有的直播配置
- **参数**:
    - 无
- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=GetRTMP`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": [{
      "id": 1
      "beg": '20150101010101',
      "end": '20150102010101',
      "record": 1,
      "file_path": '/abc',
      "push_url": ...,
      "pull_url": ...
    },
    ...
  ]
}
```


### 删除某个 RTMP 配置

- **说明**: 删除一个 RTMP 直播配置
- **参数**:
    |   参数   |    必选   |   类型    |   说明  |  
    |   :----: |   :-----: |   :-----: |   :------: |  
    |   id     |   True    |    int    |  在调用添加和获取所有配置的时候可以看到 id |  
- **示例**:
`user=apitest@cdnzz.com&token=<your token>&method=DelRTMP&id=<id>`
- **返回**:
```
{
  "error": 0,
  "msg": "",
  "result": {
      "id": 1
      "beg": '20150101010101',
      "end": '20150102010101',
      "record": 1,
      "file_path": '/abc',
      "push_url": ...,
      "pull_url": ...
  }
}
```
