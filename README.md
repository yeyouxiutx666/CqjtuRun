# FakeGPS 模拟定位工具

## https://run.yvio.cn/
## 展示网站

## 出处
本文件夹中的 FakeGPS.apk 为原版开源应用，来源：
https://github.com/Warren-Falk/FakeGPS
（模拟位置工具，通过 Android 模拟位置 API 输出假 GPS 坐标）

我们仅提供该 APK 及使用说明，未修改应用本体。

## 包含文件
- FakeGPS.apk — 原版模拟定位应用
- cqjtu_loop.kml — 重庆交通大学科学城校区田径场路线示例（WGS-84坐标）
- Magisk.apk — Root工具，仅被检测时需要
- LSPosed-v1.9.2-7024-zygisk-release.zip — Xposed框架，Magisk里刷入
- HideMockLocation.apk — 隐藏模拟定位痕迹的LSPosed模块

## 安装与使用
1. 安装 FakeGPS.apk
2. 打开后授予定位权限
3. 设置 → 开发者选项 → 选择模拟位置应用 → 选 FakeGPS
4. 把 cqjtu_loop.kml 传到手机存储（如 Download 目录）
5. 打开 FakeGPS → 点"浏览"选 cqjtu_loop.kml
6. 参数设置：
   - 速度：2.5 m/s
   - 重复次数：999（无限循环）
   - 广播间隔：1秒（默认）
   - 海拔：不填
   - 加速度：不填
7. 点开始，切到跑步小程序即可

## 是否需要 Root

### 不需要 Root
直接用就行。大部分校园跑小程序不检测模拟定位，能正常记录。

### 需要 Root 的情况
如果小程序提示"位置异常"或判定作弊，说明检测到了模拟定位。
解决：手机 Root + Magisk + LSPosed + HideMockLocation 模块，
作用域勾选系统框架和微信，重启后模拟定位痕迹会被隐藏。

所需文件（本文件夹全部提供）：
- Magisk.apk — 获取 Root 权限
- LSPosed-v1.9.2-7024-zygisk-release.zip — 在 Magisk 模块页刷入
- HideMockLocation.apk — LSPosed 模块

步骤：
1. 安装 Magisk.apk
2. 修补 boot 镜像完成 Root（需先解锁 BL）
3. 在 Magisk 模块里刷入 LSPosed.zip，重启
4. 安装 HideMockLocation.apk
5. 打开 LSPosed，启用 HideMockLocation 模块
6. 作用域勾选：系统框架 + 微信（或企业微信）
7. 重启手机

## 如何自己编写 KML 文件

KML 是 Google Earth 的地标格式，FakeGPS 读取其中的坐标点依次移动。

### 格式模板
```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2">
  <Document>
    <name>我的路线</name>
    <Placemark>
      <name>起点</name>
      <Point>
        <coordinates>106.316317,29.422810,0</coordinates>
      </Point>
    </Placemark>
    <Placemark>
      <name>第二点</name>
      <Point>
        <coordinates>106.316517,29.422717,0</coordinates>
      </Point>
    </Placemark>
    <!-- 继续加更多点... -->
  </Document>
</kml>
```

### 获取坐标方法
1. 打开高德地图网页版（ditu.amap.com）
2. 找到你要跑步的位置，右键点"这是哪里"可以看到坐标
3. 注意：高德地图用的是 GCJ-02 坐标系，FakeGPS 用的是 WGS-84
4. 如果发现路线偏移几百米，说明坐标系没转换，需要用 GCJ-02→WGS-84 转换工具

### 坐标格式说明
- 每个点格式：经度,纬度,海拔
- 经度在前，纬度在后（注意顺序别搞反）
- 海拔填0即可
- 想绕圈跑就把最后一个点设回第一个点形成闭环

### 简单做法
在高德地图上沿着跑道边缘点一圈，把每个点的坐标记下来，
按上面的格式填入 KML，保存为 .kml 文件传到手机即可。

## 常见问题：路线跑到河里去了

如果你在 FakeGPS 里选了 KML 后，打开跑步小程序发现位置不在跑道上，
而是在河里、路上或者偏了几百米，这是坐标系问题。

### 原因
- 高德地图、腾讯地图用的是 GCJ-02 坐标系（火星坐标）
- Android GPS 硬件输出的是 WGS-84 坐标系
- 两者在国内会有几百米的系统性偏移（约西370米、北300米）
- 直接拿高德坐标塞进 KML，位置就会偏到河里

### 解决
1. 先在高德地图上沿着跑道取一圈坐标
2. 用 GCJ-02 → WGS-84 转换工具转换每个点
3. 转换后的坐标再写入 KML
4. 转换后路线应该正好落在跑道上

### 验证方法
打开小程序跑10秒，看地图上蓝色轨迹是否画在粉色跑道上。
如果偏了，说明坐标没转对，重新转换。
如果轨迹在跑道上但距离不对（比如一圈显示0.1公里而不是0.4公里），
那是小程序自己的距离计算方式不同，需要把 KML 的点间距拉大一些。
