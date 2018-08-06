##  1、接口说明
-----

### 1.1接入条件
确保账号审核通过。

需要相关的开发者id和密钥，请联系商务合作经理获取。

该接口适用于2018年及以后产生的。

### 1.2接口风格
采用restfull api风格

以http状态码来表明请求资源的状态

### 1.3 接口限制
采用签名验证的方式来验证权限

验证通过后, 同一资源每天最多可请求100次

### 1.4 接口的验证
开发者审核通过后,请求接口需要带三个参数


| 参数  |  描述 |

| ----- | ---- |
| 参数          | 描述     |
| -----------    |   ---------- | 
| signature  |   加密签名，signature结合了开发者的密钥参数和请求中的timestamp                     参数、 nonce参数。      |
|  timestamp     |  时间戳    |
|  nonce         |  随机数     |
|

加密/校验流程如下：

1.将token、timestamp、nonce三个参数进行字典序排序

2.将三个参数字符串拼接成一个字符串进行sha1加密

3.开发者获得加密后的字符串可与signature对比，标识该请求合法

检验signature的PHP示例代码：

1.private function checkSignature（）     
 2.{     
     3.$signature = $_GET["signature"];    
     1. $timestamp = $_GET["timestamp"];    
     5.$nonce = $_GET["nonce"];    
     1.     
     7.$token = TOKEN;     
     8.$tmpArr = array($token, $timestamp, $nonce);     
     9.sort($tmpArr, SORT_STRING);     
     10.$tmpStr = implode( $tmpArr );  
     11.$tmpStr = sha1( $tmpStr );    
     1.   
     13.if ($tmpStr == $signature) {  
         1.  return true;  
         2.  } else {  
             16.return false;   
             17.}  
             18.}

###  1.5相应的数据格式

数据格式为json

字段说明

error错误信息，当请求有误时的提示文字

data 数据相关信息

### 1.6 http状态码 常见业务释义

| http 状态码  |  释义    |
|-------------|------------------|
|200          |获取成功            |
|400  |  请求参数有误，请检查参数|
|401    |用户验证有误,请检查验证信息  |
|403   |禁止访问,有可能超出了请求次数限制
|
## 2、开发者-应用列表接口

### 2.1 描述

获取开发者的应用列表

### 2.2 接口信息

| url | method     |    说明   |
| ------| -------|     ------  |
|developer/{devId}/app| GET |devId 为开发者id |
|

### 2.3 请求信息

|字段      |数据类型    | 说明      |
|------    |  ------  | -------  |
|{devId}   | string   | 位于url中，为开发者id |
|

### 2.4 响应

示例:

1.{  
    2.error: "",  
    3.data: [  
        4.{  
            5."app_name": "你好",  
             6."app_uuid": "testtest10241024",  
             7."os": 1,  
             8."status": 1  
             9.},  
             10. ...  
            11.]  
            12.}

data字段说明:

|名称  | 数据类型 | 说明    |
|-------| -------|  -----|
|app_uuid  | string | 应用id  |
| app_name | string | 应用名称  |
|os  |    int| 系统：1=Android,2=iOS|
|status  | int | 应用状态：0=待审核,1=审核通过,-3=审核不通过,2=暂停,3=冻结,4=封禁  |

## 3、开发者-广告位列表接口

### 3.1 描述

获取开发者的广告位列表

### 3.2 接口信息

|  url     |   method   | 说明     |
|------|  ------ | ------|
| developer/{devId}/app/{cornId} |GET  |  devId 为开发者id cornId 为应用id  |

### 3.3 请求信息

| 字段      | 数据类型  |  说明    |
|------   |  ------  |  ------|
|{devId}   | string  | 位于url中，为开发者id |
| {cornId} | string  | 位于url中，为应用id  |

### 3.4 响应

示例:

1.{  
    2.error: "",  
    3.data: [  
         4.{  
              5. "slot_name":"xxxx_banner_default",
        6."slot_uuid": "xxxxxxxx",  
        7."ad_type": 1,  
        8."status": 5  
         9.},  
         10. ...
 11.]  
 12.}

data字段说明:

| 名称    | 数据类型  | 说明      |
|-----   | ------   |  -----|
| slot_name |string  | 广告位名称 |
|slot_uuid | string |广告位ID  |
|ad_type | int  | 广告位类型：1=banner,2=插屏,3=激励视频,4=原生,5=开屏|
|status  | int  | 广告位状态：2=暂停,4-封禁,5=开启 |
|

## 4、开发者-应用统计接口

### 4.1 描述

获取开发者某应用的统计数据

### 4.2 接口信息

| url    |  method | 说明   |
|------   |  ------  |------ |
|developer/{devId}/app/{cornId}/stat  | GET   | devId 为开发者id cornId 为应用id |
|

### 4.3 请求信息

| 字段     | 数据类型    | 说明    |
|-----   |  -------- |  ----- |
| {devId} |  string | 位于url中，为开发者id |
|{cornId}  | string  |  位于url中，为应用id  |
|  start_date  | date | 开始日期,必选参数,格式为2010-01-01,闭区间 |
| end_date|  date  | 结束日期,必选参数,格式为2010-01-01,闭区间 |
|

关于日期的相关说明：

1.当天下午16:30之后,可以获取到昨天的数据

2.时间范围不可以超过三十天

### 4.4 响应

示例:

1.{  
    2.error: "",  
    3.data: [  
         4.{  
              5. "date": "2018-01-01",  
              6. "ad_type": 2,  
              7. "request": 666,  
              8."exposure": 666,  
              9."click": 666,  
              10. "click_rate": "100%",  
              11. "ecpm": "10",  
              12. "cpc": "1",  
              13. "income": 666  
              14.}  
              15.]  
              16.}

data字段说明:

| 名称  | 数据类型  | 说明  |
| ----- | ------ | -----|
| date |  string |  日期  |
|ad_type|int|广告形式：1=banner,2=插屏,3=激励视频,4=原生,5=开屏|
|request|int |请求数 |
|exposure |int| 展示数  |
| click| int |点击数  |
| click_rate  | string |点击率,保留小数点后两位,理论最大值为100. |
|ecpm | string | ecpm |
| cpc | string | CPC |
| income | float | 收益,货币的类型与用户设置的货币类型一致. |
|

## 5、合作方-应用统计接口

### 5.1 描述

获取合作方某应用的统计数据

### 5.2 接口信息

| url | method | 说明 |
| ------ | ----- | ------ |
|partner/{partnerId}/app/{cornId}/stat  | GET |  partnerId 为合作方id cornId 为应用id|
|

### 5.3 请求信息

|  字段  |  数据类型 |  说明   |
|-----|  -----  |  ----- |
| {partnerId} | string | 位于url中，为合作方id |
| {cornId} | string | 位于url中，为应用id |
| start_date | date |开始日期,必选参数,格式为2010-01-01,闭区间 |
| end_date |date |结束日期,必选参数,格式为2010-01-01,闭区间|
|

关于日期的相关说明：

1.当天下午16:30之后,可以获取到昨天的数据

2.时间范围不可以超过三十天

### 5.4 响应

示例:

1.{  
    2.error: "",  
    3.data:[   
         4.{  
             5."date": "2018-01-01"  
             6."ad_type":2,  
             7."request": 666,  
             8."exposure": 666,  
             9."click": 666,  
             10."click_rate": "100%",  
             11."ecpm": "10" ,   
             12."cpc": "1",  
              13."income": 666   
               14.}  
               15.  
               16.}

data字段说明:

| 名称   |  数据类型  | 说明   |
| -----|  ----- |  ---- |
| date  |  string |  日期   |
|ad_type|int| 广告形式：1=banner,2=插屏,3=激励视频,4=原生,5=开屏|
| request  | int | 请求数 |
| exposure  | int | 展示数 |
| click | int | 点击数 |
|  click_rate  | string | 点击率,保留小数点后两位,理论最大值为100. |
| ecpm | string | ecpm |
|  cpc | string | CPC |
| income | float | 收益,货币的类型与用户设置的货币类型一致. |
 
© 2015 ~ 2017 Power By [玉米平台开发者协议](http://doc.ssp.yumimobi.com/index.php/AppList/agreement)





