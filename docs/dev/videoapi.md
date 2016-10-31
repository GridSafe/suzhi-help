
# 速致视频转码 API 文档（新版)
------------

* 更新时间 1: 2016/10/27

## 基本说明

描述，使用 API 前需要在网站控制中心设置“转码方案”，详情请看 [这里](../user-guide/transcode/)

API 请求域名 - `http://videoapi.cdnzz.com/`

基于 http 使用 POST 方式传递参数。

具体的 API 验证方法:

  1. 首先需要通过速致 API 获取相应的 token（ [参考在线文档](http://docs.cdnzz.com/dev/api-v3/#token) ）
  2. 通过获取到的 token 进行视频转码的 API 请求

通过速致 API 获取到的 token 可以设置过期时间，过期时间内可以通过 token 去调用别的相关 API。

Python 代码示例:

```
import requests

def get_token():
    rd = dict(method="FetchToken", secretkey=key, user=user)
    rv = requests.post('http://www.cdnzz.com/apiv3/json', data=rd)
    rv = rv.json()
    token = rv['result']['token']
    return token

token = get_token()
data = dict(user=user, token=token, space='suzhi', path='/test/hello-world.mp4')
requests.post('http://videoapi.cdnzz.com/api/v1.0/video/upload', data=data,
              files={'file': open('path/to/file.mp4', 'rb')})
```

## 状态码解析
新版的APIAPI参数缺失／校验失败http-status-code不会默认返回200，而是根据相应的情况返回相应的状态码， 状态码及对应的含义如下

| http-status-code |  说明  |
| :-----:  | :-----------------------------------:  |
|  200     | 请求成功，已完成处理或已经将任务提交处理 |
|  400     | 参数缺失/非法，或token过期 |
|  401     | token校验失败，非法token |
|  403     | 用户校验失败，或使用非法空间|
|  404     | 请求处理的文件资源不存在 |
|  415     | 媒体格式错误，请求处理的文件资源不是视频文件|
|  417     | 即时请求处理任务失败 |

对于耗时的任务请求，在给用户回调内容／用户查询中, 返回消息中status字段状态码及其相应的含义

| task-status-code |  说明  |
| :-----:  | :-----------------------------------:  |
| 0        | 任务未处理，在排队等待处理中 |
| 1        | 任务处理中 |
| 2        | 任务处理成功 |
| 3        | 任务处理失败 |

## 用户回调与状态查询
对于每一个耗时的处理，API获取任务后将根据提交内容生成一个task_id, 根据这个task_id可以调用
状态查询API查询任务执行状态。
针对耗时任务，用户在提交任务时可以添加一个callback 可选的URL字段，这个callback url必须能接收post
请求，我方请求payload为json, 内容如下: 
```
{
    'task_id': '' # 任务ID
    'status': task-status-code,
    'msg': '' # 状态信息备注
}
```
虽然callback字段是可选的，但是推荐用户填写。


## 上传视频文件到存储API
- **说明**: 添加一个视频文件到存储空间
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/upload`
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
  "status": http-status-code,
  "msg": "", // 简要说明
  "result": {
      'info':"" //视频简略信息
  }
}
```

## 添加视频转码任务API
- **说明**: 选取存储中的一个文件进行转码
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/transcode`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| callback | False | string | （可选）转码成功后回调地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，文件地址必须存在
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
```
{
  "status": http-status-code,
  "msg": msg,
  "task_id": task_id,
  "result": {
    'info': "" //视频原始简略信息
  }
}
```

## 上传视频并添加转码任务API
- **说明**: 上传视频并添加转码任务
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/upload-and-transcode`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
| file | True | binary data | 视频文件的二进制数据内容 |
| callback | False | string | （可选）转码成功后回调地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址，文件地址必须存在
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
```
{
  "status": http-status-code,
  "msg": msg,
  "task_id": task_id,
  "result": {
    'info': "" //视频原始简略信息
  }
}
```


## 视频截图请求API
- **说明**: 对存储中存在的视频文件进行截图
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/screenshot`
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
  'status': http-status-code,
  "msg": "",
  "result": {
    img_sources:{
        path/3_000.jpg,
        path/4_000.jpg
    }
  }
}
```


## 视频信息请求API
- **说明**: 获取存储在存在视频的信息
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/info`
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
  'status': http-status-code,
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

## 视频资源采集API
- **说明**: 提交优酷等网络视频资源链接，将视频下载到指定存储路径
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/grab`
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
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&url=http://xxx.xxx.com/xxx/xxx.mp4
- **返回**:

```
{
  "status": http-status-code,
  "task_id": task_id,
  "msg": msg
}
```

## 视频提取音频API
- **说明**: 对存储中存在的视频进行提取音频
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/video/extract-audio`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| space | True | string | 用户存储空间名，例如 `test` |
| token | True | string | 令牌 |
|callback | False| string | （可选）提取完毕后回调地址 |

- **描述**:
    - 只有企业用户才可以使用
    - 请求地址为视频地址, 且视频必须存在
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
```
{
  "status": http-status-code,
  "task_id": task_id,
  "msg": msg,
  "result": {
     "urlpath": "*.mp3",
  }
}
```

## m3u8转mp4API
- **说明**: 提交可访问的m3u8资源链接，将其转成mp4存储到指定路径
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/m3u8/convert-m3u8-to-mp4`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| space | True | string | 用户存储空间名，例如 `test` |
| path | True | string | 最终合并成的完整视频存储路径，例如 `/path/to/video/cdnzz.mp4` |
| token | True | string | 令牌 |
| url   | True | string | m3u8链接 |
| callback | False | string | (可选）回调链接，完成转录任务后将消息回调给用户|

- **描述**
    - 只有企业用户才可以使用
    - 目前API只能将可以播放的m3u8链接转成mp4

- **返回**
```
{
    "status": http-status-code,
    "task_id": task_id,
    "msg": ""
}
```

## 后台任务查询API
- **说明**: 后台处理任务查询, 根据task_id查询任务的执行状况
- **调用地址**: `http://videoapi.cdnzz.com/api/v1.0/task/query-status`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| token | True | string | 令牌 |
| task_id   | True | string | 后台任务task_id |

- **描述**
    - 只有企业用户才可以使用

- **返回**
```
{
    "status": task-status-code,
    "task_id": "",
    "msg": "",
}
```
