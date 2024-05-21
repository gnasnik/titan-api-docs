# TITAN STORAGE API文档

更新日期: 2024-04-12

* [API基本信息](#API-基本信息)
* [HTTP返回代码](#HTTP-返回代码)
* [接口错误代码](#接口错误代码)
* [错误代码汇总](#错误代码汇总)
* [鉴权说明](#鉴权说明)
* [RESTFUL 接口](#RESTFUL-接口)
	- [获取随机数](#获取随机数)
	- [登陆](#登陆)
	- [查询是否会员](#查询是否会员)
	- [创建目录](#创建目录)
	- [获取文件和目录](#获取文件和目录)
	- [删除目录](#删除目录)
	- [查询存储空间](#查询存储空间)
	- [上传原文件](#上传原文件)
	- [上传CAR文件](#上传CAR文件)
	- [删除文件或目录](#删除文件或目录)
  - [文件下载链接](#文件下载链接)
  - [获取分享链接](#获取分享链接)
  - [设置分享状态](#设置分享状态)
* [上传文件的Demo(Javascript)](#上传文件的Demo(Javascript))


## API 基本信息

*   本篇列出接口的 base URL:
    <https://api-test1.container1.titannet.io>

## HTTP 返回代码

*   HTTP 4XX 错误码用于指示错误的请求内容、行为、格式。问题在于请求者。

*   HTTP 5XX 错误码用于指示Titan服务侧的问题。

## 接口错误代码

每个接口都有可能抛出异常，异常响应格式如下：

    {
        "code": -1,
        "err": 1004,
        "msg": "服务器繁忙，请稍后再试"
    }

## 错误代码汇总

*   1001 - 无效参数
*   1002  - 用户不存在
*   1003 - 密码不正确
*   1004 - 服务器错误


## 鉴权说明

以下所有需要鉴权接口, 除了参数之外, 还需要在请求头部添加

`Jwtauthorization: Bearer {token}`

## RESTFUL 接口

### 获取随机数

> GET /api/v1/storage/login_before

在使用钱包登陆前,需要先获取随机数

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| username | STRING | YES  | 用户名                        |
| type     | INT    | YES  | 验证码类型, 1: 注册 2: 登陆 3: 钱包签名 |

响应:

```
{"code":0,"data":{"code":"TitanNetWork(199728)"}}
```


### 登陆

> POST /api/v2/login

参数：

| 名称       | 类型     | 是否必须 | 描述  |
| -------- | ------ | ---- | --- |
| username | STRING | YES  | 钱包地址 |
| sign | STRING | YES  | 签名  |

sign: 将`login_before`的接口返回的 code 参数,使用钱包的私钥进行签名

**将参数以Json的形式放在请求的body中进行传递。**

响应:

    {
        "code": 0,
        "data": {
            "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MTE0MzgyMjksImlkIjoiNDE3MzI5MDYyQHFxLmNvbSIsIm9yaWdfaWF0IjoxNzExNDM0NjI5fQ.d6V16GWozbH6YpU1efw0RvkS35OHe_3A8Bsn72alG_k",
            "expire": "2024-03-26T15:30:29+08:00"
        }
    }

### 查询是否会员

> GET /api/v1/storage/get_vip_info

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |


响应:

```
{
    "code": 0,
    "data": {
        "vip": false
    }
}
```


### 创建目录

> GET /api/v1/storage/create_group

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |
| name | STRING | YES  | 目录名称                        |
| parent | INT | YES  | 父目录ID                        |

示例:

/api/v1/storage/create_group?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&name=hello&parent=0

响应:

```
{
    "code": 0,
    "data": {
        "group": {
            "ID": 7,
            "UserID": "0xe003b2fb03f3126347afdbba460ed39e57f9588d",
            "Name": "test",
            "Parent": 0,
            "AssetCount": 0,
            "AssetSize": 0,
            "CreatedTime": "0001-01-01T00:00:00Z"
        }
    }
}
```

### 获取文件和目录

> GET /api/v1/storage/get_asset_group_list

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |
| parent | INT | YES  | 父目录ID                        |
| page | INT | YES  | 页码                        |
| page_size | INT | YES  | 页数                        |

示例:

/api/v1/storage/get_asset_group_list?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&parent=0&page_size=10&page=1

响应:

```
{
    "code": 0,
    "data": {
        "list": [
            {
                "AssetOverview": {
                    "AssetRecord": {
                        "CID": "bafybeig2czmihhlyoo3x2erjaqb5cyief4jq6l565mht5lwfeeaaqsugwi",
                        "Hash": "1220da1658839d7873b77d12290403d161042f130f2fbeeb0f3eaec52100084a86b2",
                        "NeedEdgeReplica": 10,
                        "TotalSize": 404893,
                        "TotalBlocks": 3,
                        "Expiration": "2024-09-09T16:25:53+08:00",
                        "CreatedTime": "2024-04-12T16:25:53+08:00",
                        "EndTime": "2024-04-12T16:28:28+08:00",
                        "NeedCandidateReplicas": 1,
                        "ServerID": "edeae7b0-2639-4a0e-b733-8d66325ccd17",
                        "State": "EdgesFailed",
                        "NeedBandwidth": 0,
                        "Note": "",
                        "Source": 2,
                        "RetryCount": 1,
                        "ReplenishReplicas": 0,
                        "ReplicaInfos": [
                            {
                                "Hash": "1220da1658839d7873b77d12290403d161042f130f2fbeeb0f3eaec52100084a86b2",
                                "NodeID": "c_9bf58a0b-6d83-43d3-923b-7398ff51cb88",
                                "Status": 3,
                                "IsCandidate": true,
                                "EndTime": "2024-04-12T16:26:49+08:00",
                                "DoneSize": 404893,
                                "StartTime": "2024-04-12T16:25:53+08:00"
                            },
                            ....
                            {
                                "Hash": "1220da1658839d7873b77d12290403d161042f130f2fbeeb0f3eaec52100084a86b2",
                                "NodeID": "e_9b338f3d-8c4a-45f1-857d-9dafc483cbd4",
                                "Status": 3,
                                "IsCandidate": false,
                                "EndTime": "2024-04-12T16:28:22+08:00",
                                "DoneSize": 404893,
                                "StartTime": "2024-04-12T16:26:49+08:00"
                            }
                        ],
                        "SPCount": 0
                    },
                    "UserAssetDetail": {
                        "UserID": "0xe003b2fb03f3126347afdbba460ed39e57f9588d",
                        "Hash": "1220da1658839d7873b77d12290403d161042f130f2fbeeb0f3eaec52100084a86b2",
                        "AssetName": "aishop.png",
                        "AssetType": "file",
                        "ShareStatus": 0,
                        "Expiration": "2024-09-09T16:25:53+08:00",
                        "CreatedTime": "2024-04-12T16:25:53+08:00",
                        "TotalSize": 405027,
                        "Password": "",
                        "GroupID": 0
                    },
                    "VisitCount": 0,
                    "RemainVisitCount": 10,
                    "FilcoinCount": 0
                },,
                "Group": {
                    "ID": 5,
                    "UserID": "0xe003b2fb03f3126347afdbba460ed39Le57f9588d",
                    "Name": "test",
                    "Parent": 0,
                    "AssetCount": 0,
                    "AssetSize": 0,
                    "CreatedTime": "2024-04-12T10:13:14+08:00"
                }
            }
        ],
        "total": 1
    }gc
}
```

返回值说明:
AssetRecord
列表里有两种类型:

- AssetOverview 文件
    - AssetRecord 文件在titan网络中的相关信息
        - CID: 文件CARID
        - HASH: 文件 HASH
        - NeedEdgeReplica: 分发到边缘节点(L2)的副本数量ID
        - TotalSize: 文件大小
        - TotalBlocks: 文件的区块数量
        - NeedCandidateReplicas: 分发到候选节点(L1)的数量
        - State: 状态信息
        - ReplicaInfos: 副本的相关信息
            - NodeID: 节点ID
            - Status: 状态 0: 等待中 1: 下载中 2: 失败 3:成功
            - IsCandidate: 是否候选节点
            - DoneSize: 下载文件大小
            - EndTime: 完成时间
            - StartTime: 开始时间
    - UserAssetDetail: 文件相关信息
        - UserID:  用户ID
        - Hash: 文件 HASH
        - AssetName: 文件名称
        - AssetType: 文件类型, file: 文件, folder 文件夹
        - ShareStatus: 分享状态, 0: 未分享 1: 已分享
        - TotalSize: 文件大小 
        - Password: 设置的密码, 仅对加密文件
        - GroupID: 所属目录ID
    - VisitCount:       分享链接访问次数
    - RemainVisitCount: 分享链接剩余访问次数
    - FilcoinCount:  filecoin 备份的数量

- Group 文件夹
    - ID:  目录ID
    - UserID: 用户ID
    - Name: 目录名称
    - Parent: 目录ID
    - AssetCount: 文件数量
    - AssetSize: 文件大小(字节)
    - CreatedTime: 创建时间


### 删除目录

> GET /api/v1/storage/delete_group

**鉴权**

注意, 不能删除非空的目录.

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |
| group_id | INT | YES  | 目录ID                        |

示例:

/api/v1/storage/delete_group?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&group_id=6

响应:

```
{
    "code":0,
    "data":
    {
        "msg":"delete success"
        
    }
}
```

### 查询存储空间

> GET /api//api/v1/storage/get_linkv1/storage/get_storage_size

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |

示例:

/api/v1/storage/get_storage_size?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d

响应:

```
{
    "code": 0,
    "data": {
        "PeakBandwidth": 0,
        "TotalSize": 104857600,
        "TotalTraffic": 0,
        "UsedSize": 267370
    }
}
```

返回值说明:

-  TotalSize: 总可用存储空间
-  UsedSize:  已用存储空间

### 上传原文件  

文件上传流程包含以下三个主要步骤：
  1. 获取上传配置
  2. 执行文件上传
  3. 创建资源记录


**1. 获取上传配置** 

> GET /api/v1/storage/get_upload_info

鉴权： 需要用户验证。

请求参数： 无

成功响应示例：

```
{
    "code": 0,
    "data": {
        "UploadURL": "https://32d71de6-453a-4050-a41f-bfc7587a7500.test.titannet.io:2345/uploadv2",
        "Token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBbGxvdyI6WyJ1c2VyIl0sIklEIjoiNzU5MDUxODczQHFxLmNvbSIsIk5vZGVJRCI6IiIsIkV4dGVuZCI6IiIsIkFjY2Vzc0NvbnRyb2xMaXN0IjpudWxsfQ.x-32tjLnbCr0Q6xGLTVXiv7LJchMRXIYbupCn3djlo4",
        "NodeID": "c_32d71de6-453a-4050-a41f-bfc7587a7500",
        "AlreadyExists": false
    }
}
```

返回值说明:
- UploadURL: 用于上传的URL地址。
- Token: 访问授权令牌。


**2. 上传文件**

在获取上传配置后，使用获得的UploadURL进行文件上传。

> POST https://xxx.test-l1.titannet.io:2345/upload

请求头： 必须包含Authorization: Bearer {Token}以进行身份验证。

请求参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| file | binary | YES  | 要上传的文件内容                      |

示例:

```
curl -XPOST -H'Content-Type: multipart/form-data' -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJBbGxvdyI6WyJ1c2VyIl0sIklEIjoiNzU5MDUxODczQHFxLmNvbSIsIk5vZGVJRCI6IiIsIkV4dGVuZCI6IiIsIkFjY2Vzc0NvbnRyb2xMaXN0IjpudWxsfQ.x-32tjLnbCr0Q6xGLTVXiv7LJchMRXIYbupCn3djlo4" -F"file=@/home/gnasnik/gopath/src/flutter-call-workerd.zip" https://32d71de6-453a-4050-a41f-bfc7587a7500.test.titannet.io:2345/uploadv2
```

成功响应示例：

```
{"code":0,"err":0,"msg":"","cid":"bafkreiehumdua2ptr5udzlib3w7g7x6jbq7wewmv2qlqgfiaye5rucoyqq"}
```

字段说明：

cid: 文件的唯一标识符。


**3. 创建资源** 

在文件上传成功后，使用文件的唯一标识符（CID）来创建资源记录。

> GET /api/v1/storage/create_asset

鉴权： 需要用户验证。

请求参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                      |
| asset_cid | STRING | YES  | 文件CID                   |
| asset_name | STRING | YES  | 文件名称                 |
| asset_size | INT | YES  |     文件大小                |
| group_id | INT | YES  | 上传的文件夹目录ID            |
| asset_type |STRING | YES  | 类型（例如：file 或 folder） |                            


示例:

/api/v1/storage/create_asset?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&asset_cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm&asset_name=1702690533062.png&lang=cn&asset_size=267370&group_id=7&asset_type=file

成功响应示例：

```
{
    "code": 0,
    "data": {
        "CandidateAddr": "https://9bf58a0b-6d83-43d3-923b-7398ff51cb88.test-l1.titannet.io:2345/upload",
        "Token": "xxx"
    }
}
```

字段说明：

CandidateAddr: 资源可用的地址。
Token: 访问授权令牌。


### 上传CAR文件

上传文件分为两步:
 - 1. 创建资源
 - 2. 上传文件


**1. 创建资源** 

> GET /api/v1/storage/create_asset

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                      |
| asset_cid | STRING | YES  | 文件CID                   |
| asset_name | STRING | YES  | 文件名称                 |
| asset_size | INT | YES  |     文件大小                |
| group_id | INT | YES  | 上传的文件夹目录ID            |
| asset_type |STRING | YES  | 类型（例如：file 或 folder） |                            


示例:

/api/v1/storage/create_asset?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&asset_cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm&asset_name=1702690533062.png&lang=cn&asset_size=267370&group_id=7&asset_type=file

响应:

```
{
    "code": 0,
    "data": {
        "CandidateAddr": "https://9bf58a0b-6d83-43d3-923b-7398ff51cb88.test-l1.titannet.io:2345/upload",
        "Token": "xxx"
    }
}
```

返回值说明:
- CandidateAddr: L1 的上传URL
- Token: 身份凭证


**2. 上传文件**

获取第一步的上传URL之后, 使用 POST 将文件上传到 L1 节点.
注意, 在上传时,请在 Header 中添加 `Authorization: Bearer {token}` 来

> POST https://xxx.test-l1.titannet.io:2345/upload

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| file | binary | YES  | 文件的二进制                      |


响应:

```
{
    "code":0,"
    err":0,
    "msg":"Upload succeeded"
}
```

### 删除文件或目录

> GET /api/v1/storage/delete_asset

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |
| asset_cid | STRING | YES  | 文件CID                        |

示例:

api/v1/storage/delete_asset?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&asset_cid=bafybeibcuqfjzkwnvf4bj2tpe3bufjehz7gaold6llvujrz2sur4rcs3fe

响应:

```
{
    "code":0,
    "data":
    {
        "msg":"delete success"
        
    }
}
```

## 文件下载链接

> GET /api/v1/storage/share_asset

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户ID                        |
| asset_cid | STRING | YES  | 文件CID                        |

示例:

/api/v1/storage/share_asset?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&asset_cid=bafkreig6bbd2abr2taznhsbgnbmgjwhqldhcxcpktgs7mgahz5lx2cgkpa

响应:

```
{
    "code": 0,
    "data": {
        "asset_cid": "bafkreig6bbd2abr2taznhsbgnbmgjwhqldhcxcpktgs7mgahz5lx2cgkpa",
        "redirect": true,
        "url": "https://9bf58a0b-6d83-43d3-923b-7398ff51cb88.test-l1.titannet.io:2345/ipfs/bafkreig6bbd2abr2taznhsbgnbmgjwhqldhcxcpktgs7mgahz5lx2cgkpa?token=xxxxNqT2jI\u0026filename=auth.go"
    }
}
```

返回值说明:
- asset_cid: 文件的 CAR CID
- url: 文件的链接, 可以直接访问

**在url后面拼接&download=true ,返回二进制流格式**

### 获取分享链接

> GET /api/v1/storage/get_link

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| username | STRING | YES  | 用户名                        |
| cid | STRING | YES  | 文件CID                        |
| url | STRING | YES  | 文件CID                        |


 现阶段,url 需要使用titan存储域名拼接,如下面的示例

示例:

/api/v1/storage/get_link?cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm&url=https://storage.titannet.io/distributionstatus?cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm&AssetName=1702690533062.png&AssetType=file&address=0xe003b2fb03f3126347afdbba460ed39e57f9588d&username=0xe003b2fb03f3126347afdbba460ed39e57f9588d

响应:

```
{
    "code": 0,
    "data": {
        "url": "/link?cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm"
    }
}
```

**此时仅生成分享链接,并未对外公开,需要再次调用设置分享状态**

最终的分享链接为titan存储的域名拼接 返回的 url:

比如: https://storage.titannet.io/link?cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm


### 设置分享状态

> GET /api/v1/storage/share_status_set

**鉴权**

参数：

| 名称       | 类型     | 是否必须 | 描述                         |
| -------- | ------ | ---- | -------------------------- |
| user_id | STRING | YES  | 用户名                        |
| cid | STRING | YES  | 文件CID                        |

示例:

/api/v1/storage/share_status_set?user_id=0xe003b2fb03f3126347afdbba460ed39e57f9588d&cid=bafybeih4kcycijywcmnnfdz2e7cyxrfwjnkb2ddlhyiqs3c4un7efg6atm

响应:

```
{
    "code":0,
    "data":
    {
        "msg":"delete success"
        
    }
}
```


## 上传文件的Demo(Javascript)

```
<template>
  <div>
    <input type="file" @change="handleFileUpload">
    <button @click="uploadFile">上传</button>
  </div>
</template>

<script>
import axios from 'axios'
import { createFileEncoderStream, CAREncoderStream } from 'ipfs-car'
// import { QuickerClient } from 'quicker';

export default {
  name: 'HomeView',
  data() {
    return {
      selectedFile: null,
      rootCID:null,
      myFile:null
    };
  },
  methods: {

    handleFileUpload(event) {
      this.selectedFile = event.target.files[0];
      console.log('select file:', this.selectedFile);

      var req = "http://abc.asset.titannet.io:2345/ipfs/bafybeiammv47altsbq24swakw3b2tepdel3qqrhajvnjbtt23vg7zm6dim"
      axios.get(req)
      .then(response => {
          console.log('get :', response);
          })
        .catch(error => {
          console.log('Error:', error);
      });

      let myFile
      // 转成car
      createFileEncoderStream(this.selectedFile)
        .pipeThrough(new TransformStream({
          transform: (block, controller) =>{
            this.rootCID = block.cid
            controller.enqueue(block)
            
          // console.log("root:" + this.rootCID.toString())
          }
        }))
        .pipeThrough(new CAREncoderStream())
        .pipeTo(new WritableStream({
            write(chunk){
              if (myFile == undefined) {
                myFile = chunk
              } else {
                let mergedArray = new Uint8Array(myFile.length + chunk.length);
                mergedArray.set(myFile);
                mergedArray.set(chunk, myFile.length);
                myFile = mergedArray
              }
              // console.log("=write:", chunk);
            },
            close:() => {
              // 在这里处理数据写入完成的逻辑
              // 
              this.myFile = myFile
              console.log("convert to car, rootCID:%s, file len:%d", this.rootCID, this.myFile.length);
            },
            abort(error) {
              // 在这里处理写入过程中发生的错误
              console.error(error);
            }
        }));

        // console.log("root:" + this.rootCID.toString())

    },
    uploadFile() {
      if (!this.selectedFile) {
        alert('请选择要上传的文件');
        return;
      }

      console.log("root:" + this.rootCID.toString())
      let webURL = "http://192.168.0.119:8080/api/v1/storage/create_asset?user_id=123&asset_cid="+this.rootCID.toString()+"&asset_name="+this.selectedFile.name+"abc&asset_size=" + this.myFile.length

      axios.get(webURL)
      .then(response => {
          console.log('create asset:', response);

          let myURL = response.data.data.CandidateAddr
          let config = {
              headers: {
                "Authorization": "Bearer "+ response.data.data.Token,
              }
          }

          let blob = new Blob([this.myFile]);
          const formData = new FormData();
          formData.append('file', blob);

          axios.post(myURL, formData, config)
          .then(response => {
              // 处理上传成功的响应
              console.log('upload :', response);
              alert('文件上传成功');
            })
            .catch(error => {
              // 处理上传失败的响应
              console.log('Error:', error);
              alert('文件上传失败');
            });

          })

        .catch(error => {
          // 处理上传失败的响应
          console.log('Error:', error);
          alert('create asset failed');
      });
    }
  }
};
</script>

```
