---
title: 'android 获取sim卡运营商信息(转)'
layout: post
tags:
    - tech
    - android
    - java
---
TelephonyManager tm = (TelephonyManager)Context.getSystemService(Context.TELEPHONY_SERVICE);

注意：一些电话信息需要相应的权限。

```java
// 获取服务提供商名字，比如电信，联通，移动用下面的方法第一种方法: 获取手机的IMSI码,并判断是中国移动\中国联通\中国电信
getSimOperatorName()  //Returns the Service Provider Name (SPN).
```

IMSI 国际移动用户识别码（IMSI：International Mobile Subscriber Identification Number）是区别移动用户的标志， 储存在SIM卡中，可用于区别移动用户的有效信息。

IMSI由MCC、MNC、MSIN组成，

其中MCC为移动国家号码，由3位数字组成 // 唯一地识别移动客户所属的国家，我国为460；

MNC为网络id，由2位数字组成// 用于识别移动客户所归属的移动网络，中国移动为00，中国联通为01,中国电信为03；

MSIN为移动客户识别码，采用等长11位数字构成 //唯一地识别国内GSM移动通信网中移动客户。

可以看出IMSI在MIN号码前加了MCC，可以区别出每个用户的来自的国家，因此可以实现国际漫游。

在同一个国家内，如果有多个CDMA运营商，可以通过MNC来进行区别。

所以要区分是移动还是联通，只需取得SIM卡中的MNC字段即可。

```java
String imsi = telManager.getSubscriberId();  
  
if(imsi!=null){  
  
    if(imsi.startsWith(“46000″) || imsi.startsWith(“46002″))  
  
    {  
  
    //因为移动网络编号46000下的IMSI已经用完，所以虚拟了一个46002编号，134/159号段使用了此编号 //中国移动  
  
    }else if(imsi.startsWith(“46001″)){  
  
    //中国联通  
  
    }else if(imsi.startsWith(“46003″)){  
  
    //中国电信  
  
    }  
  
}  
```

第二种方法:

```java
TelephonyManager telManager = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);  
  
String operator = telManager.getSimOperator();  
  
if(operator!=null){  
  
    if(operator.equals(“46000″) || operator.equals(“46002″)|| operator.equals(“46007″)){  
  
    //中国移动  
  
    }else if(operator.equals(“46001″)){  
  
    //中国联通  
  
    }else if(operator.equals(“46003″)){  
  
    //中国电信  
  
    }  
  
}  
```

在文件AndroidManifest.xml中添加权限其他方法具体使用方法请查看API 文档TelephonyManager中方法说明。

在文件 AndroidManifest.xml 中添加权限 `<uses-permission android:name= "android.permission.READ_PHONE_STATE"/>`

功能 说明

`getCellLocation()` 返回的单元格位置的装置 ACCESS_COARSE_LOCATION或ACCESS_FINE_LOCATION

`getDeviceId()` 返回的IMEI / MEID的设备。 如果该设备是GSM设备然后IMEI号将被退回，如果该设备是一个CDMA设备然后MEID 将被退回 READ_PHONE_STATE

`getLine1Number()` 返回设备的电话号码（MSISDN号码） READ_PHONE_STATE

`getNetworkOperatorName()` 返回注册的网络运营商的名字

`getNetworkOperator()` 返回的MCC +跨国公司的注册网络运营商

`getNetworkCountryIso()` 返回注册的网络运营商的国家代码

`getSimCountryIso()` 返回SIM卡运营商的国家代码 READ_PHONE_STATE

`getSimOperator()` 返回SIM卡运营商的单个核细胞数+冶 READ_PHONE_STATE

`getSimOperatorName()` 返回SIM卡运营商的名字 READ_PHONE_STATE

`getSimSerialNumber()` 返回SIM卡的序列号 READ_PHONE_STATE

`getNetworkType()` 返回网络设备可用的类型。 这将是下列其中一个值：

TelephonyManager.NETWORK_TYPE_UNKNOWN

TelephonyManager.NETWORK_TYPE_GPRS

TelephonyManager.NETWORK_TYPE_EDGE

TelephonyManager.NETWORK_TYPE_UMTS READ_PHONE_STATE

`getPhoneType()` 返回设备的类型。

这将是以下值之一：

TelephonyManager.PHONE_TYPE_NONE

TelephonyManager.PHONE_TYPE_GSM

TelephonyManager.PHONE_TYPE_CDMA READ_PHONE_STATE

`getSubscriberId()` 返回用户识别码（的IMSI）的设备 READ_PHONE_STATE

`getNeighboringCellInfo()` 返回NeighboringCellInfo类代表名单相邻小区的信息，如果可用，否则将返回null ACCESS_COARSE_UPDATES