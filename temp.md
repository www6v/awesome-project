
# 日志服务协议格式

## 说明

### 日志服务分为4种类型,通过公共头部type 标识：
#### 1 call.init 通话开始 ，加入房间后调用
#### 2 call.stats 房间种 通话状态上报 
#### 3 call.leave 通话结束，退出房间后调用
#### 4 call.operation  通话操作（暂不使用）

### 协议结构
{
    commonheader,
    data
}
协议分为公共头部和数据部分，具体内容如下：

## 公共头部分

***公共头部分***

|     Key     |  ValType  |  Value  |  Explain  |
| :---------: | :-------: | :-----: |:--------: |
| version     | string | 1.0 | 协议版本，1.0 |
| method      | string | "logup" | 固定方法名|
| rpc_id      | string | 必须是字符串 | 标记本次请求 |
| type        | int | log分类 取值 1 2 3 | 1 通话开始 2 通话状态 3 通话结束  |
| ts        | long | urtc timestamp sec | 状态收集时间  |
| aid      | string | 开发者ID，必须是字符串 | 开发者ID |
| rid     | string | 房间ID，必须是字符串 | 房间ID |
| sid     | string | 本次通话标识 | 通话标识 |
| uid     | string | 用户ID，必须是字符串 | 从属于app_id的用户的ID |
| streamid   | string | 流名（字符串） | 流的标识 |
| stype | int | 订阅流还是发布流 | 1: 发布流，2: 订阅流 |
| mtype | int | 媒体流类型 | 1: 摄像头，2: 桌面流 |

***示例：公共头部分***

``` json
{
    "version": "1.0",
    "method": "xxx",
    "rpc_id": "xxx",
    "type": 1,
    "ts": 22222233232,
    "mtype": 1,
    "aid": "xxx",
    "rid": "xxx",
    "uid": "xxx",
    "sid": "xxx",
    "streamid": "xxx",
    "stype": "xxx"
}
```

***回复公共头部分***

|     Key     |  ValType  |   Value   |  Explain  |
| :---------: | :-------: | :-------: | :-------: |
| version     | string | 1.0 | 协议版本，1.0 |
| err         | int    | 0 或者 非0| 0 成功，非0失败 |
| msg         | string | 任意字符串 | 对err的文字描述内容 |
| method      | string | ack | 回复信令 必须是 ack |
| rpc_id      | string | 与发起端的rpc_id相同 | 标记本次请求 |

***示例：回复的公共头部分***

``` json
{
    "version": "1.0",
    "err": 0,
    "msg": "success",
    "method": "ack",
    "rpc_id": "xxx",
}
```

******

## 呼叫开始（type 1 call.init）

*** 关键参数***

|     Key     |  ValType  |  Explain  |
| :---------: | :-------: | :-------: |
| devinfo       | string | 设备信息json 格式串的base64编码 |

***call.init 示例***

``` json
{
    "version": "1.0",
    "method": "xxx",
    "rpc_id": "xxx",
    "type": 1,
    "ts": 22222233232,
    "mtype": 1,
    "aid": "xxx",
    "rid": "xxx",
    "uid": "xxx",
    "sid": "xxx",
    "streamid": "xxx",
    "stype": "xxx",
    "data": {
        "devinfo": "xxx"
    }
}
```

*** device info json 结构***

|     Key     |  ValType  |  Explain  |
| :---------: | :-------: | :-------: |
| sdkv       | string 1.2.3 主版本.小版本.构建次数 | sdk 版本号 |
| agent      | string "web_windows web_android windws android ios mac "等等  | sdk 类型 |
| device      | string 厂商_品牌_型号  比如 apple_iphone_iphonexr | 设备类型类型 |
| system      | string 系统描述 系统版本_平台架构  比如 android9.0_armv8a | 系统描述 |
| network      | string 网络类型 unkown lan wifi 2g 3g 4g 5g mobile | 网络类型 |
| cpu      | string cpu 描述 品牌_型号_核数 比如intel_xxxxx_4  | cpu 描述 |
| mem      | int 内存大小  单位（MB） | 内存大小 |


``` json
{
    "sdkv":"",
    "agent":"web_windows",
    "device":"apple_iphone_iphonexr",
    "system": "android9.0_armv8a",
    "network": "wifi",
    "cpu":"intel_xxxxx_4",
    "mem": 8192
}
```

## 回复 call.init

*** 无私有参数***

***call.init 回复示例***

``` json
{
    "version": "1.0",
    "err": 0,
    "msg": "success",
    "method": "ack",
    "rpc_id": "xxx",
    "data": { }
}

```

******

## 通话状态上报 （type 2 call.stats）

***data 部分参数***

|     Key     |  ValType  |  Explain  |
| :---------: | :-------: | :-------: |
| rtt       | int | 往返时延  只针对发布里|
| delay       | int | 播放时延=（jitter + render + decode + system delay + rtt） 只针对订阅流 |
| br  | int    | 码率  音频 视频 |
| lostpre      | int    | 音视频分别的丢包率 |
| vol         | int | 音频能量 |
| frt         | int | 视频帧率 |
| w         | int | 视频宽度 |
| h         | int | 视频高度 |
| mime       | string | 编码类型 |

***call.stats 示例***

``` json
{
   "version": "1.0",
    "method": "xxx",
    "rpc_id": "xxx",
    "type": 2,
    "ts": 22222233232,
    "mtype": 1,
    "aid": "xxx",
    "rid": "xxx",
    "uid": "xxx",
    "sid": "xxx",
    "streamid": "xxx",
    "stype": "xxx",
    "data": {
        "rtt": 200, //pub 有效
        "delay":130, //sub 有效
        "audio":{
            "br":  64000,
            "lostpre": 12,
            "vol": 30,
            "mime":"opus"  
        },
        "video": {
            "br":  300000,
            "lostpre": 12,
            "frt": 13, 
            "w": 640,
            "h": 360,
            "mime":"vp8" 
        }
    }
}
```

## 回复 call.stats

*** 无私有参数***

***call.stats 回复示例***

``` json
{
    "version": "1.0",
    "err": 0,
    "msg": "success",
    "method": "ack",
    "rpc_id": "xxx",
    "data": { }
}

```

******

## 通话结束 3 （call.leave）

*** 无私有参数***

*** call.leave 请求示例 ***
``` json
{
    "version": "1.0",
    "method": "xxx",
    "rpc_id": "xxx",
    "type": 3,
    "ts": 22222233232,
    "mtype": 1,
    "aid": "xxx",
    "rid": "xxx",
    "uid": "xxx",
    "sid": "xxx",
    "streamid": "xxx",
    "stype": "xxx",
    "data": {
    }
}
```
## 回复 call.leave

*** 无私有参数***

***call.leave 回复示例***

``` json
{
    "version": "1.0",
    "err": 0,
    "msg": "success",
    "method": "ack",
    "rpc_id": "xxx",
    "data": { }
}

```
## 通话操作 （type 3 call.opertion） 暂无表述
