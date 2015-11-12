
# 速致视频转码 API 文档
------------

* 更新时间 1: 2015/08/20
* 更新时间 2: 2015/11/10

## 基本说明

注意，使用 API 前需要在网站控制中心设置“转码方案”，详情请看 [这里](../user-guide/transcode/)

API 请求域名 - `https://avapi.cdnzz.com/`

基于 HTTPS 使用 POST 方式传递参数。

具体的 API 验证方法:

  1. 首先需要通过速致 API 获取相应的 token（ [参考在线文档](https://docs.cdnzz.com/dev/api-v3/#token) ）
  2. 通过获取到的 token 进行视频转码的 API 请求

注意，通过速致 API 获取到的 token 可以设置过期时间，过期时间内可以通过 token 去调用别的相关 API。

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

## 视频文件上传
- **说明**: 添加一个视频文件到存储空间并进行具体的转码处理
- **调用地址**: `https://avapi.cdnzz.com/video/upload`
- **参数**:

| 参数  |     必选  |   类型  | 说明 |
| :-----:  | :---: | :----:  | :-----------------------------------:  |
| user | True | string | email 或者 用户名 user_name |
| path | True | string | 具体文件的存储路径，例如 `/path/to/video/file.mp4` |
| file | True | binary data | 视频文件的二进制数据内容 |

- **注意**:
    - 只有企业用户才可以使用
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename&file=<video binary data\>
- **返回**:

```
{
  "error": 0,
  "msg": "",
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

- **注意**:
    - 只有企业用户才可以使用
- **示例**:
user=apitest@cdnzz.com&token=<token\>&space=space&path=/video/filename
- **返回**:
其中 address 为对应上传文件 hls 转码后对应的文件访问入口。

```
{
  "error": 0,
  "msg": "",
  "result": {
    'status': 0 or 1,
    'urlpath': "*.m3u8"
  }
}
```

