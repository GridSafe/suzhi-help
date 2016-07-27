
# 速致视频转码 API 文档
------------

* 更新时间 1: 2015/08/20
* 更新时间 2: 2015/11/10
* 更新时间 3: 2015/12/03

## 基本说明

描述，使用 API 前需要在网站控制中心设置“转码方案”，详情请看 [这里](../user-guide/transcode/)

API 请求域名 - `https://avapi.cdnzz.com/`

基于 HTTPS 使用 POST 方式传递参数。

具体的 API 验证方法:

  1. 首先需要通过速致 API 获取相应的 token（ [参考在线文档](https://docs.cdnzz.com/dev/api-v3/#token) ）
  2. 通过获取到的 token 进行视频转码的 API 请求

描述，通过速致 API 获取到的 token 可以设置过期时间，过期时间内可以通过 token 去调用别的相关 API。

Python 代码示例:

```
import requests

def get_token():
    rd = dict(method="FetchToken", secretkey=key, user=user)
    rv = requests.post('https://www.cdnzz.com/apiv3/json', data=rd)
    rv = rv.json()
    token = rv['result']['token']
    return token

token = get_token()
data = dict(user=user, token=token, space='suzhi', path='/test/hello-world.mp4')
requests.post('https://avapi.cdnzz.com/video/upload', data=data,
              files={'file': open('path/to/file.mp4', 'rb')})
```

## 视频文件上传自动转码
- **说明**: 添加一个视频文件到存储空间并进行具体的转码处理
- **调用地址**: `https://avapi.cdnzz.com/video/upload`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| file | True | binary data | 视频文件的二进制数据内容 |
| callback | False | string | （可选）视频转码后进行信息回调的地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 对于 callback 参数，当提供一个可用回调 url，我们的转码程序会在视频文件处理完成后进行回调通知，回调 url 必须能访问且接收 POST 的请求，请求内容为包括 <path, user, space\> 信息的 JSON 格式序列化数据
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&file=<video binary data\>
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "",
  "result": {
      'info':"" //视频简略信息
  }
  "session-id": "" //该次转码留存key
}
```

## 视频转码确认
- **说明**: 添加一个视频到存储中进行转码
- **调用地址**: `https://avapi.cdnzz.com/video/confirm`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，不是m3u8文件地址
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 urlpath 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
    'urlpath': "*.m3u8"
    'info': "" //视频原始简略信息
  }
}
```


## 视频截图请求
- **说明**: 对已上传好的视频进行截图请求
- **调用地址**: `https://avapi.cdnzz.com/video/screenshot`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| seek | True | string | 截图起始秒数，例如`20` |
| count | False | string | 截图数量，如果无此参数则请求单张截图 |
| offset | False | string | 多张截图时间间隔，如果count大于1,则该参数必选，如果count无参数，则该参数无效 |
| reverse | False | string | 多张截图时，选择正向或反向截图，仅在reverse存在且为1时采用逆序截图 |

- **描述**:
    - 只有企业用户才可以使用
    - 暂时不支持m3u8
    - 当请求多张截图时，仅返回视频范围内的截图
    - seek 与 offset 本身以秒为单位，但均可使用浮点数例如 `1.1`, `3.1415`, 当使用浮点数时，可以表示毫秒单位的时间。
    - 返回图片命名以视频截图秒数为名，类似请求位于3.1415秒的截图，返回的即是3_142.jpg，默认小数精度保留3位。
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&seek=seek&count=count&offset=offset&reverse=1
- **返回**:


```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
    img_sources:{
        path/3_000.jpg,
        path/4_000.jpg
    }
  }
}
```


## 视频信息请求
- **说明**: 对已上传好的视频进行信息请求
- **调用地址**: `https://avapi.cdnzz.com/video/information`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| is_full | False | string | 仅当该参数为1时，返回全部信息。 默认返回简单信息。 |

- **描述**:
    - 只有企业用户才可以使用
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&is_full=1
- **返回**:


```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
    "bit_rate": "265680", 
    "duration": "1437.467000", 
    "height": 240, 
    "size": "47738423", 
    "width": 320
  }
}
```

## 视频文件上传
- **说明**: 添加一个视频文件到存储空间,不进行任何处理
- **调用地址**: `https://avapi.cdnzz.com/avhub/video/upload`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| file | True | binary data | 视频文件的二进制数据内容 |

- **描述**:
    - 只有企业用户才可以使用
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&file=<video binary data\>
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "",
  "result": {
      'info':"" //视频简略信息
  }
  "session-id": "" //该次转码留存key
}
```

## 提交视频链接自动下载
- **说明**: 添加一个视频文件的链接, 后台自动下载都指定的存储目录
- **调用地址**: `https://avapi.cdnzz.com/video/download`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| url | True | string | 视频文件的链接地址 |
| callback | False | string | （可选）视频下载信息回调的地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 经过测试可以下载的视频完整包括优酷/网易视频/腾讯视频等主流的视频网站(2016.06.15)
    - 对于 callback 参数，当提供一个可用回调 url，我们的转码程序会在视频文件处理完成后进行回调通知，回调 url 必须能访问且接收POST的请求，回调内容为包括 <user, savepath, url, status, msg\> 信息的 JSON 格式序列化数据
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&url=http://xxx.xxx.com/xxx/xxx.mp4
- **返回**:

```
{
  "status": 0, //3-完成, 4-下载失败, 5-合并失败
  "msg": "", // 成功为ok, 或者时出错提示
  "result": {
  }
}
```
- **回调callback post数据格式**
```
{
    "user": "", //调用接口的用户
    "savepath": "", //下载视频的存储地址
    "url": "", //提交下载的原始url
    "status": 0, //3-完成, 4-下载失败, 5-合并失败
    "msg": "", //成功提示, 出错信息
}
```

## 查询视频采集进度
- **说明**: 和添加链接自动下载接口配套使用, 查询视频下载的进度状态
- **调用地址**: `https://avapi.cdnzz.com/video/query-download-status`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| url | True | string | 视频文件的链接地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 参数要和提交视频链接自动下载接口的中相应的参数一致
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&url=http://xxx.xxx.com/xxx/xxx.mp4
- **返回**:

```
{
    "user": "", //调用接口的用户
    "savepath": "", //下载视频的存储地址
    "url": "", //提交下载的原始url
    "status": 0, //0-未处理, 1-下载中, 2-合并中, 3-完成, 4-下载失败, 5-合并失败
    "msg": "", //成功提示, 出错信息
}
```

## 添加视频转码任务
- **说明**: 对存储中存在的视频进行转码
- **调用地址**: `https://avapi.cdnzz.com/video/transcode`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| callback | False | string | （可选）视频转码后进行信息回调的地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 对于 callback 参数，当提供一个可用回调 url，我们的转码程序会在视频文件处理完成后进行回调通知，回调 url 必须能访问且接收 POST 的请求，请求内容为包括 <path, user, space\> 信息的 JSON 格式序列化数据
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:

```
{
  "status": 0, //0为成功，其他为错误
  "msg": "",
  "result": {
      "info": {},//视频简略信息
      "space": ""
  }
  "session-id": "" //该次转码留存key
}
```


## 视频提取音频
- **说明**:对已存储的视频进行音频提取
- **调用地址**: `https://avapi.cdnzz.com/avhub/audio/extract`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，不是m3u8文件地址
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 urlpath 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
     "urlpath": "*.mp3",
  }
}
```

## 视频提取音频确认
- **说明**:对已存储的视频转码为音频确认
- **调用地址**: `https://avapi.cdnzz.com/avhub/audio/confirm`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，不是m3u8文件地址
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 urlpath 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  'status': 0 or 1,
  "msg": "",
  "result": {
     "urlpath": "*.mp3",
  }
}
```

## 视频分片上传接口
- **说明**: 视频分片上传接口, 接收POST请求, 将POST请求的视频分片保存到存储, 可将大视频分成小片上传到存储, 在用合并接口将其合并成原始视频.
- **调用地址**: `https://avapi.cdnzz.com/video/chunk/upload`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| space | True | string | 用户存储空间名，例如 `test` |
| path | True | string | 最终合并成的完整视频存储路径，例如 `/path/to/video/cdnzz.mp4` |
| token | True | string | 令牌 |
| range | True | string | 上传视频分片的range, 格式0-4194304|
| checksum | True | string | 上传视频分片的md5值 |
| file  | True | binaryData | 视频分片的二进制数据内容 |

- **描述**:  
    - 只有企业用户才可以使用   
    - range参数中, 0-4194304表示startByte - endByte(不包含该byte) 。例如要将一个大文件分成每片大小40M的分片进行上传, 则第一片seek(offset=0), range为0-41943040,
第二片seek(offset=41943040), range为41943040-83886080, 依此类推, 其中最后一片的右值为文件的大小  
    - 同一文件的同一分片可以多次上传, 假如上传的分片在存储已经存在一个md5值一致的分片, 则认为该分片已经上传完成, 否则将新提交的分片覆盖旧分片. 

- **返回**:
```
{
    "status": 0, // 0-分片上传中, 1-分片上传成功, 2-分片已经存在,无须重复上传
    "msg": "", // 上传返回信息
    "info": {  // 只有在分片上传成功时, 该字段才不会空
        "range": "" // 本次上传片段的range
        "md5sum":  // 本次上传分片文件内容的md5值
    }
}
```

## 视频分片上传查询接口
- **说明**: 视频分片查询接口, 接收POST请求, 查询已经成功上传到存储的分片信息, 用户可以根据该接口自行判断视频分片是否已经全部完整的上传到存储
- **调用地址**: `https://avapi.cdnzz.com/video/chunk/list`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| space | True | string | 用户存储空间名，例如 `test` |
| path | True | string | 最终合并成的完整视频存储路径，例如 `/path/to/video/cdnzz.mp4` |
| token | True | string | 令牌 |

- **描述**
    - 只有企业用户才可以使用
    - 建议在分片合并之前调用该接口检查一下是否已经将全部分片上传完毕

- **返回**
```
{
    "status": 0 // 0表示成功, 其他为失败
    "msg": "" // 查询失败的原因
    "chuncks": [
        {
            "range": "0-4096", // 分片range
            "md5sum": "", //该分片文件内容的md5值
        },
        {
            "range": "4096-8192", // 分片range
            "md5sum": "", // 该分片文件内容的md5值
        },
        ...
    ]
}
```

## 分片视频合并接口
- **说明**: 在确认所有的视频分片已经全部完整的上传到存储后, 可调用该接口对这些分片将视频合并成一个完整的视频.
- **调用地址**: `https://avapi.cdnzz.com/video/chunk/merge`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 最终合并成的完整视频存储路径，例如 `/path/to/video/cdnzz.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| checksum| True | string | 完整视频的md5值 |
| callback | False | string	|（可选）视频分片合并完成后回调的地址

- **描述**
    - 只有企业用户才可以使用
    - 假设用户提供了回调接口的地址, 必须确认该接口能接收POST请求
- **回调callback POST参数**
```
{
     "user": "", //调用接口的用户
     "status": 2,  // 2-合并成功, 3-合并完成,但格式检查失败, 4-文件md5值校验失败
     "msg": "", // 解释信息
     "info": {
         "savepath": //  完整视频在存储中的存储位置
         "size":  , // 完整视频的大小
         "md5sum" : "" // 完整视频的md5值
    }
}
```

- **返回**
```
{
    "status": 2,  // 2-合并成功, 3-文件格式检查失败, 4-文件md5值校验失败
    "msg": "", //  解释信息
    "info": {
        "savepath": "" // 完整视频在存储中的存储位置
        "size":  , // 完整视频的大小, bytes
        "md5sum" : "" // 完整视屏的md5值
    }
}
```

## 视频分片合并状态查询接口
- **说明**: 视频分片合并状态查询接口, 接收POST请求, 可查询视频分片合并状态
- **调用地址**: `https://avapi.cdnzz.com/video/chunk/query-merge-status`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| space | True | string | 用户存储空间名，例如 `test` |
| path | True | string | 最终合并成的完整视频存储路径，例如 `/path/to/video/cdnzz.mp4` |
| token | True | string | 令牌 |

- **描述**
    - 只有企业用户才可以使用

- **返回**
```
{
    "user": "" //查询的用户
    "status": 0 // 0-正在等待合并, 1-合并中, 2-合并成功, 3-文件格式检查失败, 4-文件md5校验失败
    "msg": "" // 详细信息
    "info": { // 若合并成功, 则放回视频信息
        "savepath": "" // 完整视频在存储中的存储位置
        "size":  , // 完整视频的大小, bytes
        "md5sum" : "" // 完整视屏的md5值
    }
}
```
