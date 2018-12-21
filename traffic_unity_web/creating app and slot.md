# 1. Introduction

### 1.1 Prerequisites

- Please make sure of your account has activated.

- You have got the Developer ID and Key. If not, please contact at global@yumimobi.com for getting these.

### 1.2 Interface Style

- restfull api

- Use http status code to indicate the status of request resources

### 1.3 Interface limitation

- Signature is used to verify permission

- The same resource is limited to request 100 times a day

### 1.4 Interface Verification

After verification is approved, developers need to pass 3 parameters in their requests:

| Parameters | Description |
| ----- | ----- |
| signature | Encrypted signature, combining developer's key parameters with timestamp and nonce in requests|
| timestamp | Timestamp |
| nonce | Random number |


Encryption/examination process:

    1. Sort token, timestamp and nonce in alphabetical order
    
    2. Put the three parameter strings in one string to encrypt with sha1
    
    3. Compare the encrypted string with signature, identify the request as legitimate


The code sample of checking signature in Golang:

    func Sha1Encode(str string) string {
        sha := sha1.New()
        sha.Write([]byte(str))
        bs := sha.Sum(nil)
    
        return fmt.Sprintf("%x", bs)
    }
    
    // check signature
    func CheckSignatureRule(token, signature, timestamp, nonce string) bool {
        tmp_arr := []string{token, timestamp, nonce}
        sort.Strings(tmp_arr)
        tmp_str := strings.Join(tmp_arr, "")
        signature_real := Sha1Encode(tmp_str)
    
        if signature != signature_real {
            return true
        }
    
        return false
    }

### 1.5 Data Format of Response

- JSON

- Field instruction

    - error error message, prompt when quest is incorrect

    - data data related information

### 1.6 http status code and explanations

| http status code | Explanation |
| ------------|---------- |
| 200 | Success|
| 400 | Request parameter error, please check parameters |
| 401 | User verification error, please check verification information |
| 403 | Access denied, possibly because of request limitation |

# 2. Creating applications
### 2.1 Note
 This interface is for creating applications and is limited to request 100 times a day. 

### 2.2 Request information

|URL|Method|Description|
|------|------|------|
|report_api/developer/ssp/{dev_uuid}/app|POST|dev_uuid is your Developer ID|

Sample of request URL：

    http://imweb.yumimobi.com/report_api/developer/ssp/yumi5pc8djb37vzjl0zr/app?signature=a815ebea59d5e223f19cd1598f8ccd1adf4ed25m&timestamp=1528707294&nonce=84

### 2.3 Request instruction
#### 2.3.1 Parameter of path
|Parameter|Type|Description|Mandatory|
|-------|-------|-------|-------|
|{dev_uuid}|string|dev_uuid is your Developer ID and is in request URL|yes|

#### 2.3.2 Parameter of body
|Parameter|Type|Description|Mandatory|
|-------|-------|-------|-------|
|os_channel|int64|the market which your apps launched. 1=Google Play, 2=App Store, 3=Android market of China|yes|
|join_style|int|the integration mode. 1=SDK, 2=API|yes|
|cate_first_id|int64|first category ID, [category](#6-category)|yes|
|cate_id|int64|second category ID, [category](#6-category)|yes|
|app_store_id|int64|the Android market of China: <br>the value is 1 when os_channel is 1, it means market is Google Play; <br>the value is 27 when os_channel is 2, it means the market is iTunes App Store; <br>the value is specific ID from  [Android Market of China](#5-android-market-of-china) when os_channel is 3, it means the market is the Chinese Android Market;|yes|
|download_url|string|download URL|yes|
|name|string|the application name|yes|
|bundle|string|bundle name or package name|yes|
|icon_url|string|download URL of application icon, if this field is null, we will use the default icon https://static-test.zplay.cn/web4/ssp/img/yumi.png |no|

Sample of request：

    {
        "name": "linkstar",
        "join_style": 2,
        "os_channel": 1,
        "bundle": "com.zplay.Linking",
        "download_url": "https://play.google.com/store/apps/details?id=com.zplay.Linking",
        "cate_first_id": 26,
        "cate_id": 62,
        "app_store_id": 1,
        "icon_url": "https://pa-cloud.zplayads.com/spider_man/2018/07/05/bcv0kcmpeft2n7aqs010"
    }

### 2.4 Response instruction

|Paremeter|Type|Description|
|-------|-------|-------|
|error|string|it will be null if the request is successful or it will show the message when the request is failed.|
|name|string|the application name|
|app_uuid|string|the ID of app that you registered through request, this ID is created by YUMI platform.|
|status|int|the status of app auditing. 0=Not Audit, 1=approval, -3=refused, 2=Suspended, 3=Frozen，4=Banned|

Sample of response：

    {
        "error": "",
        "data": {
            "name": "linkstar",
            "app_uuid": "k5pukapy",
            "status": 1
        }
    }

# 3. Getting status of auditing 
### 3.1 Note

This interface is for getting the applications status of auditing. The max numbers of request is 100 times a day and you can get not great than 100 applications auditing status in each request.

### 3.2 Request information
|URL|Method|Description|
|-----|-----|-----|
|/report_api/developer/ssp/{dev_uuid}/app|GET|dev_uuid is your Developer ID|

Sample of request URL：

    http://imweb.yumimobi.com/report_api/developer/ssp/yumi5pc8djb37vzjl0zr/app?signature=a815ebea59d5e223f19cd1598f8ccd1adf4ed25m&timestamp=1528707294&nonce=84&app_uuid=k5pukapy,4j0qu4ac,l6x65z75

### 3.3 Request instruction

|Parameter|Type|Description|Mandatory|
|-----|-----|-----|-----|
|{dev_uuid}|string|dev_uuid is your Developer ID and is in request URL|yes|
|app_uuid|string|the app ID, not great than 100 applications in request and split by ','|yes|

### 3.4 Response information

|Parameter|Type|Description|
|-------|-------|-------|
|error|string|it will be null if the request is successful or it will show the message when the request is failed.|
|name|string|the application name|
|app_uuid|string|the ID of app that you registered through request, this ID is created by YUMI platform.|
|status|int|the status of app auditing. 0=Not Audit, 1=approval, -3=refused, 2=Suspended, 3=Frozen，4=Banned|

Sample of response:

    {
        "error": "",
        "data": [
            {
                "name": "popstar_1",
                "app_uuid": "3on76zm2",
                "status": 1
            },
            {
                "name": "popstar_2",
                "app_uuid": "9sv1a62a",
                "status": 1
            },
            {
                "name": "popstar_3",
                "app_uuid": "rll0vmer",
                "status": 1
            }
        ]
    }


# 4. Creating slot
### 4.1 Note
This interface is for creating slot. THe request is limited 100 times a day and you can create 5 slots in each request.

### 4.2 Request information
|URL|Method|Description|
|-----|-----|-----|
|report_api/developer/ssp/{dev_uuid}/slot|POST|dev_uuid is your Developer ID|

Sample of request URL：

    http://imweb.yumimobi.com/report_api/developer/ssp/yumi5pc8djb37vzjl0zr/slot?signature=a815ebea59d5e223f19cd1598f8ccd1adf4ed25m&timestamp=1528707294&nonce=84

### 4.3 Request instruction

#### 4.3.1 Parameter of path
|Parameter|Type|Description|Mandatory|
|-----|-----|-----|-----|
|{dev_uuid}|string|dev_uuid is your Developer ID and is in request URL|yes|

#### 4.3.2 Parameter of body
|Parameter|Type|Description|Mandatory|
|-----|-----|-----|-----|
|app_uuid|string|the app ID. the app ID must be same if you create many slots(should bot great than 5) in a request.|yes|
|name|string|the name of slot|yes|
|type|int|the type of slot, 1=banner, 2=interstitial, 3=rewarded video, 4=native ,5=splash|yes|
|refresh_duration|int|the interval to refresh(second): <br>this field must not be null when the value of type is 1, the default is 30 seconds, you may set the value to 15 seconds or 45 seconds；<br>this field should be null if the value of type is not 1|no|
|img|int|whether or not the native slot needs picture: <br>this field will be optional when the value of type is 4, 0=not needed；1=needed；<br>this field should be null if the value of type is not 4|no|
|img_width|int|width of picture, this field is mandatory when img is 1|no|
|img_height|int|height of picture, this field is mandatory when imge is 1|no|
|icon|int|whether or not the native slot needs icon: <br>this field will be optional when the value of type is 4, 0=not needed；1=needed；<br>this field should be null if the value of type is not 4|no|
|title|int|whether or not the native slot needs title: <br>this field will be optional when the value of type is 4, 0=not needed；1=needed；<br>this field should be null if the value of type is not 4|no|
|data|int|whether or not the native slot needs description: <br>this field will be optional when the value of type is 4, 0=not needed；1=needed；<br>this field should be null if the value of type is not 4|no|


Sample of response：

    [{
        "app_uuid": "7jcddpp6",
        "name": "slot_1",
        "type": 4,
        "img": 1,
        "img_width": 300,
        "img_height": 300,
        "icon": 1,
        "title": 1,
        "data": 0
    }, {
        "app_uuid": "7jcddpp6",
        "name": "slot_2",
        "refresh_duration": 30,
        "type": 1
    }]

### 4.4 Response information


|Parameter|Type|Description|
|-------|-------|-------|
|error|string|it will be null if the request is successful or it will show the message when the request is failed.|
|uuid|string|the slot ID|
|name|string|the slot name|
|type|int|the slot type, 1=banner, 2=interstitial, 3=rewarded video, 4=native, 5=splash|

Sample of response:

    {
        "error": "",
        "data": [
            {
                "uuid": "9akdjup8",
                "name": "slot_1",
                "type": 4,
            },
            {
                "uuid": "4ueksds3",
                "name": "slot_2",
                "type": 1,
            }
        ]
    }



# 5. Android Market of China

|Market ID|Name|Os_channel<br>(1=Google Play, 2=App Store, 3=Android Market of China)|
|-------|-------|-------|
|1|Google play|1|
|2|	Application treasure store|3|
|3|	Application of TreasureBaidu Handset Assistant|3|
|4|	360 Handset Assistant|3|
|5|	Wandoujia |3|
|6|	Kupai Application Store|3|
|7|	Anzhi |3|
|8|	HUAWEI Application Store|3|
|9|	91 Assistants|3|
|10| Mobile MM|3|
|11|PP Assistants|3|
|12|UC Game Center|3|
|13|189 Store|3|
|14|GFan|3|
|15|Zhuoyi Market|3|
|16|Millet application store|3|
|17|MEIZU Application Store|3|
|18|Lenovo Mobile Market |3|
|19|Jinli Software Store|3|
|20|OPPO Software Store|3|
|21|Jinli Game Center|3|
|22|VIVO|3|
|26|Others|3|
|27|App Store|2|


# 6. Category

<table>
    <tr>
        <td>First Category ID</td> 
        <td>First Category Name</td> 
        <td>Second Category ID</td> 
        <td>Second Category Name</td> 
   </tr>
   <tr>
        <td>1</td> 
        <td>News</td> 
        <td>34</td> 
        <td>News</td> 
   </tr>
   <tr>
        <td>2</td> 
        <td>Library</td> 
        <td>35</td> 
        <td>Library</td> 
   </tr>
   <tr>
        <td>3</td> 
        <td>Social</td> 
        <td>36</td> 
        <td>Social</td> 
   </tr>
   <tr>
        <td>4</td> 
        <td>Life</td> 
        <td>37</td> 
        <td>Life</td> 
   </tr>
   <tr>
        <td>5</td> 
        <td>Finance</td> 
        <td>38</td> 
        <td>Finance</td> 
   </tr>
   <tr>
        <td>6</td> 
        <td>Entertain</td> 
        <td>39</td> 
        <td>Entertain</td> 
   </tr>
   <tr>
        <td>7</td> 
        <td>Education</td> 
        <td>40</td> 
        <td>Education</td> 
   </tr>
   <tr>
        <td>8</td> 
        <td>Travel</td> 
        <td>41</td> 
        <td>Travel</td> 
   </tr>
   <tr>
        <td>9</td> 
        <td>Navigation</td> 
        <td>42</td> 
        <td>Navigation</td> 
   </tr>
   <tr>
        <td>10</td> 
        <td>Business</td> 
        <td>43</td> 
        <td>Business</td> 
   </tr>
   <tr>
        <td>11</td> 
        <td>Tool</td> 
        <td>44</td> 
        <td>Tool</td> 
   </tr>
   <tr>
        <td>12</td> 
        <td>Efficiency</td> 
        <td>45</td> 
        <td>Efficiency</td> 
   </tr>
   <tr>
        <td>13</td> 
        <td>Fitness</td> 
        <td>46</td> 
        <td>Fitness</td> 
   </tr>
   <tr>
        <td>14</td> 
        <td>Camera</td> 
        <td>47</td> 
        <td>Camera</td> 
   </tr>
   <tr>
        <td>15</td> 
        <td>Product Guide</td> 
        <td>48</td> 
        <td>Product Guide</td> 
   </tr>
   <tr>
        <td>16</td> 
        <td>Food and Drink</td> 
        <td>49</td> 
        <td>Food and Drink</td> 
   </tr>
   <tr>
        <td>17</td> 
        <td>Reference</td> 
        <td>50</td> 
        <td>Reference</td> 
   </tr>
   <tr>
        <td>18</td> 
        <td>Magazine</td> 
        <td>51</td> 
        <td>Magazine</td> 
   </tr>
   <tr>
        <td>19</td> 
        <td>Sports</td> 
        <td>52</td> 
        <td>Sports</td> 
   </tr>
   <tr>
        <td>20</td> 
        <td>Weather</td> 
        <td>53</td> 
        <td>Weather</td> 
   </tr>
   <tr>
        <td>21</td> 
        <td>Medical Treatment</td> 
        <td>54</td> 
        <td>Medical Treatment</td> 
   </tr>
   <tr>
        <td>22</td> 
        <td>Music</td> 
        <td>55</td> 
        <td>Music</td> 
   </tr>
   <tr>
        <td>23</td> 
        <td>Video</td> 
        <td>56</td> 
        <td>Video</td> 
   </tr>
   <tr>
        <td>24</td> 
        <td>Shopping</td> 
        <td>57</td> 
        <td>Shopping</td> 
   </tr>
   <tr>
        <td>25</td> 
        <td>Traffic</td> 
        <td>58</td> 
        <td>Traffic</td> 
   </tr>
    <tr>
        <td rowspan="50">26</td>    
        <td rowspan="50">Game</td>  
        <td>59</td> 
        <td>Action Game</td> 
    </tr>
    <tr>
        <td >60</td>  
        <td >Puzzle Game</td>
    </tr>
    <tr>
        <td >61</td>  
        <td >Card Game</td>
    </tr>
    <tr>
        <td >62</td>  
        <td >Leisure Game</td>
    </tr>
    <tr>
        <td >63</td>  
        <td >Race Game</td>
    </tr>
    <tr>
        <td >64</td>  
        <td >Role-playing Game</td>
    </tr>
    <tr>
        <td >65</td>  
        <td >Strategy Game</td>
    </tr>
    <tr>
        <td >67</td>  
        <td >Arcade Game</td>
    </tr>
    <tr>
        <td >68</td>  
        <td >Party Game</td>
    </tr>
    <tr>
        <td >69</td>  
        <td >Adventure Game</td>
    </tr>
    <tr>
        <td >70</td>  
        <td >Simulation Game</td>
    </tr>
    <tr>
        <td >71</td>  
        <td >Word Game</td>
    </tr>
    <tr>
        <td >72</td>  
        <td >Quiz Game</td>
    </tr>
    <tr>
        <td >73</td>  
        <td >Table Game</td>
    </tr>
    <tr>
        <td >74</td>  
        <td >Casino</td>
    </tr>
    <tr>
        <td >75</td>  
        <td >Education</td>
    </tr>
    <tr>
        <td >76</td>  
        <td >Child</td>
    </tr>
    <tr>
        <td >77</td>  
        <td >Sports</td>
    </tr>
    <tr>
        <td >78</td>  
        <td >Music</td>
    </tr>
</table>
