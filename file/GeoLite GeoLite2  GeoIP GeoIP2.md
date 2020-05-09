# GeoLite GeoLite2  GeoIP 
1. GeoLite2
  - GeoLite2数据库是免费的IP地理位置数据库，可与MaxMind的GeoIP2数据库相比，但准确性较差。GeoLite2国家，城市和ASN数据库每周两次更新。
  - IP地址的实际位置可能在此半径以及经纬度坐标定义的区域内。
  - 商业免费
2. 关系
- geoip 是geoIP2的旧版
- geolite2 是 geoip2的商业开源版
- geolite2离线数据库每月更新一次，

3. 数据库地址
```
# Database URL
https://download.maxmind.com/app/geoip_download?edition_id=GeoLite2-City-CSV&license_key=YOUR_LICENSE_KEY&suffix=zip

# SHA256 URL
https://download.maxmind.com/app/geoip_download?edition_id=GeoLite2-City-CSV&license_key=YOUR_LICENSE_KEY&suffix=zip.sha256
```

4. 定位服务对比
[IP定位数据服务对比](https://zhuanlan.zhihu.com/p/55075356)