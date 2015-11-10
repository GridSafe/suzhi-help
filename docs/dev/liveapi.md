# 速致直播 API 文档
------------

* 更新时间 1: 2015/11/10

## RTMP 直播配置

### 添加 RTMP

- **说明**: 添加一个指定时间的 RTMP 直播配置
- **参数**:
    - beg: 开始时间，格式为 `%Y%m%d%H%M%S` 如，20151011010101
    - end: 结束时间，格式为 `%Y%m%d%H%M%S`
    - record: 是否进行录制，默认为 0，表示不录制，1 表示录制
    - file_path: 录制的指定路径
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
    - id: 在调用添加和获取所有配置的时候可以看到 id
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
