# TITAN STORAGE API文档

更新日期: 2024-04-12

[TOC]

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

### 查询是否VIP用户 

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

### 获取目录列表 

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
                "AssetOverview": null,
                "Group": {
                    "ID": 5,
                    "UserID": "0xe003b2fb03f3126347afdbba460ed39e57f9588d",
                    "Name": "test",
                    "Parent": 0,
                    "AssetCount": 0,
                    "AssetSize": 0,
                    "CreatedTime": "2024-04-12T10:13:14+08:00"
                }
            }
        ],
        "total": 1
    }
}
```

返回值说明:

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

### 查询存储空间/可用存储空间

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

### 上传文件

上传文件分为两步:
 - 1. 获取上传文件节点配置
 - 2. 上传文件


**1. 获取上传文件节点配置** 

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
| asset_type |STRING | YES  | 类型: file: 文件, folder: 文件夹 |                            


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

### 删除文件/文件夹

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
