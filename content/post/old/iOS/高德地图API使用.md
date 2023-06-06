---
title: 高德地图API使用
date: 2018-05-30 14:40:56
updated: 2018-05-30 18:42:20
comments: true
tags: [iOS]
categories:
- 学习笔记
keywords: 
permalink: 
---

## 引入头文件
```objc
#import <AMapFoundationKit/AMapFoundationKit.h>
#import <AMapLocationKit/AMapLocationKit.h>
```
## 调用方法
```objc
NSString *strKey = @"2d8a96d668576584acebf2bab0ba0c08";//默认值
//配置里面取值
NSString *strBundleKey = [[NSBundle mainBundle] infoDictionary][@"IOSPostionkey"];
if (strBundleKey) {
    strKey = strBundleKey;
}
[AMapServices sharedServices].apiKey = strKey;

// 带逆地理信息的一次定位（返回坐标和地址信息）
self.GaodelocationManager = [[AMapLocationManager alloc] init];
[self.GaodelocationManager setDesiredAccuracy:kCLLocationAccuracyHundredMeters];
//   定位超时时间，最低2s，此处设置为2s
self.GaodelocationManager.locationTimeout =10;
//   逆地理请求超时时间，最低2s，此处设置为2s
self.GaodelocationManager.reGeocodeTimeout = 10;
//设置不允许系统暂停定位
[self.GaodelocationManager setPausesLocationUpdatesAutomatically:NO];
//    //设置允许在后台定位
//    [locationManager setAllowsBackgroundLocationUpdates:YES];
//设置允许连续定位逆地理
[self.GaodelocationManager setLocatingWithReGeocode:YES];
//    [locationManager setDelegate:self];
// [self.locationManager startUpdatingLocation];
//    // 带逆地理（返回坐标和地址信息）。将下面代码中的 YES 改成 NO ，则不会返回地址信息。
[self.GaodelocationManager requestLocationWithReGeocode:YES completionBlock:^(CLLocation *gaodeLocation, AMapLocationReGeocode *regeocode, NSError *error) {

    if (gaodeLocation==nil) {
        NSLog(@"高德没有返回地理位置,使用苹果官方定位经纬度");
        [self GetGISInfoByByLocationWithjingdu:jingdu AndWeidu:weidu];
        return ;
    }

    if (error)
    {
        NSLog(@"locError:{%ld - %@};", (long)error.code, error.localizedDescription);

        if (error.code == AMapLocationErrorLocateFailed)
        {
            NSLog(@"AMapLocationErrorLocateFailed高德没有返回地理位置,使用苹果官方定位经纬度");
            [self GetGISInfoByByLocationWithjingdu:jingdu AndWeidu:weidu];
            return;
        }
    }

    NSLog(@"使用高德location:%@", gaodeLocation);
    NSString * gaodeWeidu =  [NSString stringWithFormat:@"%.9f",gaodeLocation.coordinate.latitude];
    NSString * gaodeJingdu =  [NSString stringWithFormat:@"%.9f",gaodeLocation.coordinate.longitude];
    if (regeocode)
    {
        NSLog(@"reGeocode:%@", regeocode);
        self.theRealAdress = regeocode.formattedAddress;
    }
    [self GetGISInfoByByLocationWithjingdu:gaodeJingdu AndWeidu:gaodeWeidu];
}];
```
