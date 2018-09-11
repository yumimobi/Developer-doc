##  1、Integration Instructions
-----

### 1.1 Requirements

- Please make sure of your account has activated.

- You have got the Developer ID and token. If not, please contact at global@yumimobi.com for getting these.

- This interface applies to data generated in 2018 and later.

### 1.2 Interface Style

- Restfull api.

- Use http status code to indicate the status of request resources.

### 1.3 Interface limit

- Signature is used to verify permission.

- The same resource is limited to request 100 times a day.
> The same resource means the same request URL, such as https://im.yumimobi.com/report_api/developer/{devId}/app/{cornId}/stat?signature=cfeb660899275497c8b3c7c1107981f7980abdee&timestamp=1533635260&nonce=111.


### 1.4 Interface Verification

After verification is approved, developers need to pass 3 parameters in their requests:

| Parameter             | Description     |
| -----------    |   ---------- | 
| signature  |   Encrypted signature, combining developer's token parameters and nounce in requests                     |
|  timestamp     | Unix timestamp eg. 1534305711 [Reference address](https://tool.lu/timestamp/)   |
|  nonce         |  Random number, should be a positive integer     |


**Encryption/examination process：**

1. token, timestamp and nonce in alphabetical order

2. Put the three parameter strings in one string to encrypt with sha1

3. Compare the encrypted string with signature, identify the request as legitimate

*Check the PHP sample code in signature：*

```php
    private function checkSignature（）     
    {     
     $signature = $_GET["signature"];    
     $timestamp = $_GET["timestamp"];    
     $nonce = $_GET["nonce"];    
          
     $token = TOKEN;     
     $tmpArr = array($token, $timestamp, $nonce);     
     sort($tmpArr, SORT_STRING);     
     $tmpStr = implode( $tmpArr );  
     $tmpStr = sha1( $tmpStr );    
      
     if ($tmpStr == $signature) {  
        return true;  
      } else {  
        return false;   
      }  
     }
```

###  1.5 Data Format of Response

- json

- Field instruction

    - error: error message, prompt when request is incorrect

    - data: data related information

### 1.6 http status code and explanations

| http status coed  |  explanation    |
|-------------|------------------|
|200          |success            |
|400  | Request parameter error, please check parameters |
|401    |  User verification error, please check verification information|
|403   | Access denied, probably because of request limit |

## 2、Developer - Applist Interface

### 2.1  Description

- Get developer's applist

### 2.2  Interface Information

| url | method     |    Description  |
| ------| -------|     ------  |
|http://im.yumimobi.com/report_api/developer/{devId}/app| GET |devId is developer id |


### 2.3 Request information

|Field      |Data type   | Description      |
|------    |  ------  | -------  |
|{devId}   | string   | devid located in url |


### 2.4  Response

Sample:

```json
    {  
    error: "",  
     data: [  
       {  
        "app_name": "hello",  
        "app_uuid": "testtest10241024",  
        "os": 1,  
        "status": 1  
       },  
        cell2  
      ]  
    }
```

**Data field instruction:**

|Name  | Data type | Description    |
|-------| -------|  -----|
|app_uuid  | string | App id  |
|app_name | string | App name  |
|os  |    int| opereating system: 1=Android, 2=iOS|
|status  | int | App status: 0=Pending approval, 1=Approved, -3=Not approved, 2=Pause, 3=Frozen, 4=Banned |

## 3、Developer - Slotlist Interface

### 3.1 Description

- Get slotlist of specific APP

### 3.2 Interface Information

|  url     |   method   | Description     |
|------|  ------ | ------|
| http://im.yumimoi.com/report_api/developer/{devId}/app/{cornId} |GET  |   devId is developer id, cornId is the app id in Applist Interface |

### 3.3 Request information

| Field      | Data type  |  Description    |
|------   |  ------  |  ------|
|{devId}   | string  | devid located in url |
|{cornId} | string  |  app id located in url |

### 3.4 Response

Sample:
```json
    {  
    error: "",  
      data: [  
        {  
         "slot_name":"xxxx_banner_default",
         "slot_uuid": "xxxxxxxx",  
         "ad_type": 1,  
         "status": 5  
      },  
      ...
      ]  
     }
```
**Data field instruction:**

| Name    | Date type  | Description      |
|-----   | ------   |  -----|
|slot_name |string  | Slot name |
|slot_uuid | string |Slot id |
|ad_type | int  | Slot type: 1=banner, 2=Interstitial, 3=Video, 4=Native, 5=splash|
|status  | int  | Slot status: 2=Pause, 4-Banned, 5=Open |


## 4、Developer - Application Statistics Interface

### 4.1 Description

- get statistics data of an application for developer

### 4.2  Interface Information

| url    |  method | Description   |
|------   |  ------  |------ |
|http://yumimobi.com/report_api/developer/{devId}/app/{cornId}/stat  | GET   | devId is developer id, cornId is the app id in Applist Interface |

### 4.3  Request Information

| Field     | Data type    | Description   |
|-----   |  -------- |  ----- |
| {devId} |  string | Devid located in url |
|{cornId}  | string  |  App id loacated in url  |
| start_date  | date | Required parameter indicating start date, format as 2018-01-01 |
| end_date|  date  | Required parameter indicating end date, format as 2018-01-01 |


**About date：**

- Data of the day is available 

- Time period is limited to 30 days

### 4.4 Response

Sample:
```json
    {  
    error: "",  
    data: [  
      {  
         "date": "2018-01-01",  
         "ad_type": 2,  
         "request": 666,  
         "exposure": 666,  
         "click": 666,  
         "click_rate": "100%",  
         "ecpm": "10",  
         "cpc": "1",  
         "income": 666,
         "slot_uuid": "g4fn4hv3", 
         "ad_plat_name": "yumi", 
         "country_zh": "中国"
         "country_en": "China"
         "country_code": "CN"
       }  
     ]  
    }
```
**Data field instruction:**

| Name  | Date type  | Description  |
| ----- | ------ | -----|
| date |  string |  Date  |
|ad_type|int|Ad type: 1=banner, 2=interstitial, 3=video, 4=native, 5=splash|
|request|int | Amount of requests|
|exposure |int| Amount of impressions  |
| click| int | Amount of clicks|
| click_rate  | string |Click rate, rounded to the nearest hundredth, theoretical maximum value is 100. |
|ecpm | string | eCPM |
| cpc | string | CPC |
| income | float | Income, type of currency is consistent with user's set up. |
| slot_uuid | string | Slot ID. |
| ad_plat_name | string | Advertising platform name. |
| country_zh | string | Country name in Chinese. |
| country_en | string | Country name in English. |
| country_code | string | Country code. |

## 5、Partner - Application Statistics Interface

### 5.1 Description

- get statistics data of an application for partner

### 5.2 Interface Information

| url | method | Description |
| ------ | ----- | ------ |
|http://im.yumimobi.com/report_api/partner/{partnerId}/app/{cornId}/stat  | GET | partnerId is partner id, cornId is the app id in Applist Interface |


### 5.3 Request Information

|  Description  |  Date type |  Description   |
|-----|  -----  |  ----- |
| {partnerId} | string | Partner id located in url|
| {cornId} | string | App id loacated in url|
| start_date | date |Required parameter indicating start date, format as 2010-01-01 |
| end_date |date |Required parameter indicating end date, format as 2010-01-01|


About date：

- Data of the day is available 

- Time period is limited to 30 days


### 5.4 Response

Sample:
```json
    {  
    error: "",  
    data:[   
      {  
        "date": "2018-01-01"  
        "ad_type": 2,  
        "request": 666,  
        "exposure": 666,  
        "click": 666,  
        "click_rate": "100%",  
        "ecpm": "10" ,   
        "cpc": "1",  
        "income": 666,
        "slot_uuid": "g4fn4hv3", 
        "ad_plat_name": "yumi", 
        "country_zh": "中国"
        "country_en": "China"
        "country_code": "CN"
      }  
     ]  
    }
```

**Data field instruction:**

| Name   |  Date type  | Description   |
| -----|  ----- |  ---- |
| date  |  string |  Date   |
|ad_type|int| Ad type: 1=banner, 2=interstitial, 3=video, 4=native, 5=splash|
| request  | int | Amount of requests |
| exposure  | int | Amount of impressions |
| click | int | Amount of clicks |
| click_rate  | string |  Click rate, rounded to the nearest hundredth, theoretical maximum value is 100.|
| ecpm | string | eCPM |
|  cpc | string | CPC |
| income | float |  Income, type of currency is consistent with user's set up.|
| slot_uuid | string | Slot ID. |
| ad_plat_name | string | Advertising platform name. |
| country_zh | string | Country name in Chinese. |
| country_en | string | Country name in English. |
| country_code | string | Country code. |
 
© 2015 ~ 2018 Power By YUMIMOBI [Developer Agreement](http://doc.ssp.yumimobi.com/index.php/AppList/agreement)





