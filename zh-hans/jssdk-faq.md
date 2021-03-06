# 常见问题

## 1. 使用相关

#### 1.1 能否提供测试？

您可以直接访问 [altizure.github.io/sdk.examples/examples.sdk.html](https://altizure.github.io/sdk.examples/examples.sdk.html) 来直接尝试各种范例的效果。把代码仓库 clone 到本地后即可修改其中的代码进行测试。

#### 1.2 本地测试时无法访问相关模型？

请检查以下配置是否正确：

* 是否已经启动一个 http 服务器？最方便的方法是参考 [altizure.github.io/sdk.examples/](https://altizure.github.io/sdk.examples/) 教程中用 python 建立 http 服务器的方法。
* 本地测试请用 `127.0.0.1` 作为地址来访问，而不要使用 `localhost`。

#### 1.3 无法访问 3D SDK script 链接？

如果无法访问 `https://www.altizure.com/sdk` 或 `https://beta.altizure.com/sdk`，请使用中国站点 `https://www.altizure.cn/sdk` 或 `https://beta.altizure.cn/sdk`。

#### 1.4 详细文档在哪儿？

请直接参考范例的代码，里面有详细注释。我们会持续更新相关范例，展示最新功能。

#### 1.5 如何获取用户令牌？

请直接参考 [GraphQL API](api.md) 的 `5. 获取用户令牌` 章节。

#### 1.6 Altizure 的三维引擎是用什么语言开发的？

开发语言是 JavaScript，最底层三维的接口是 WebGL。但是我们并不经常直接访问 WebGL 底层接口，我们更多的模块是搭建在 [ThreeJS](https://threejs.org/) 之上。

#### 1.7 能否自定义一些 three.js 的对象然后插入到 Altizure JavaScript 3D SDK 的场景里？

暂时不能。因为实景模型的数据量非常巨大，我们的 SDK 对数据的加载和内存管理做了许多优化，暂时未能允许插入任意的 three.js 对象。我们会逐步实现这一点，但现在并无具体上线这个功能的时间表。

#### 1.8 我是否需要学习图形学的知识才能用 Altizure JavaScript 3D SDK 编程？

基本不需要，我们尝试把这些复杂的技术实现都进行了封装。您只需要懂得网页编程所需要的 JavaScript 和 HTML 就可以进行开发。更重要的是要清晰知道实景三维模型和您的业务是如何结合的。

## 2. 常见用例

#### 2.1 点击模型中某个建筑物，怎么来获取对应的信息，如建筑物的名称？

可以通过鼠标事件获得经纬度，再去相关数据库中查建筑物名称。相关建筑物数据库由开发者自行管理。

#### 2.2 能否直接插入 OBJ 模型？

可以。如果网络下载带宽不高，过大的 OBJ 文件可能导致下载时间过长。而且因为受限于浏览器的性能，过大的 obj 也会导致浏览器崩溃。我们一般推荐 obj 模型连同纹理文件不要超过 2 MB。

OBJ 模型需要满足以下要求：

* OBJ 模型需要由开发者自行找网络空间存储并获取直接访问的 https 链接。
* 其中 OBJ 对应的 mtl 文件内的纹理路径需要是相对路径。
* OBJ 只有三角形面。
* OBJ 模型里没有非2-流形的点和边，并且没有面积为 0 的面。

#### 2.3 如果需要更换一个 marker 的 click 事件，应该怎么操作？需要把前一个绑定事件解绑吗？

最好解绑，用 `.off(event, func)`

#### 2.4 如何实现分图层显示不同的 marker 及其他的模型？

可以在打开和关闭图层的时候从图层数据库中读取相关信息，然后将相关 marker 插入到 Sandbox 或者从中删除。如果不想每次开关图层反复插入和删除相关 marker ，可以用 `marker.visible=true` 或者 `marker.visible=false` 控制每个 marker 显示与否。

#### 2.5 如何实现测量距离？

通过鼠标事件分别获取两次点击的坐标点，然后求两点距离。

#### 2.6 如何加入一个带格式的文本标签

`TextTagMarker` 是一个带简单格式的文字标签，参考 [范例2.6](https://altizure.github.io/sdk.examples/2-6-add-textTag/)。

如果您需要更复杂的格式定义，您可以用 js 创建一个 canvas, 写入带格式的文本，把 canvas 转为 image, 传给 Tag。(可以把文字和图标都画到这张canvas上。)

#### 2.7 如何获得三维场景拖动事件

响应 Sandbox 对象的 `cameraChange` 事件，`sandbox.on('cameraChange', callback)`。

#### 2.8 地图框选的功能，希望能够在地图上画个方形或圆形，然后可以获取到画的图形的范围值

pick 鼠标 down 和 up 的点,
* pick 到的两个点作为矩形的对角两个端点，矩形的边平行于经纬方向, 使用 polygon marker 绘制矩形
* 以 pick 到的两个点作为直径，中点做圆心，还是用 polygon 就可以，比如画一个 360 边形。已知圆心经纬度，和直径，顶点位置可以很容易算出来。

#### 2.9 如何删除一个 marker ?

`marker.destruct()` 可以完全销毁一个 marker，这个 marker 所用的资源将被释放。如果您想反复显示和隐藏这个 marker，可以设定这个参数 `marker.visible=true`。请参考 2.4 的解答。

#### 2.10 平台的坐标系是什么？

WGS84，

如果您在使用过程中位置出现了数百米的偏差，那很大概率是由于坐标系的问题，详细的解释请参考 `3. 关于坐标` 章节。

#### 2.11 如果我需要通过方法调用实现放大，或缩小，接口是什么？

更改相机高度 `alt`，参考范例第五章。

#### 2.12 如果我需要通过方法调用旋转、平移，接口是什么？

更改相机经纬度 `lat/lng` 实现平移，`tilt` 更改俯角（向下的与水平方向的夹角），`north` 更改方位角（与正北方向顺时针的夹角）。请参考范例第五章。

#### 2.13 flyTo方法，是否有飞行速度参数？

有。`flyTo: function (position, speed)`。`speed` 是一个速度的相对倍率，例如设做 2 的话，就是默认速度的 2 倍。

#### 2.14 水面高度设置？

水面高度是在 Altizure 主站上定义的, SDK 目前不允许修改。

#### 2.14 如何加载矢量图层数据？

您需要读取、解析您所使用的矢量数据，转化为 SDK 提供的点线面，加入场景中。

#### 2.15 如何实现局部区域透明显示设置？

您可以使用 Altizure 主站的 [裁剪](https://blog.altizure.cn/2017/03/08/altizure-cropping-3D-models/index.html) 功能。

#### 2.16 如何实现消息窗口功能？（类似点击弹出信息窗口）

使用 JavaScript 和 HTML 写好您想显示的弹窗，再和 marker 的鼠标事件绑定，比如 mouseover 或 click 时显示。

#### 2.17 如何移动 marker，或者绑定 GPS 设备的坐标？

只需要修改 marker 的坐标即可移动 marker。需要开发者通过客户端或服务器和不同设备通信获取设备的 GPS 坐标，便可直接把该坐标设做相关 marker 的坐标，这样就确保 marker 反映了设备的 GPS 位置。可以参考[演示应用](jssdk-demo.md)中的演示 2.1。

这里所说的坐标是 WGS84 坐标系下的，因此在将使用这一坐标的 marker 与百度、高德等基于国测局坐标的国内地图厂商的底图进行结合时会产生偏差，谷歌则不受此影响。详情请参考本章 `3. 关于坐标` 部分。

#### 2.18 Sandbox 里的 pid 是什么？

pid 是 project id 的简写。 浏览 Altizure 的 url (例如，[https://www.altizure.com/project/59fe68cebb619d03c446fe85/model](https://www.altizure.com/project/59fe68cebb619d03c446fe85/model) ) 里面的 `59fe68cebb619d03c446fe85` 就是 pid。

#### 2.19 如何初始化 Sandbox 的相机 (camera) 参数？

这里的 camera 参数，是指 Sandbox 初始化的时候的相机位置。它可以通过 SDK 得到，参考 [范例 5.1](https://altizure.github.io/sdk.examples/5-1-camera-pose)。camera 的参数是以经纬度，高度定义的。经纬度可以从谷歌（要和您坐标系一致，请注意国内地图厂商所使用的国测局坐标与 GPS 所用的 WGS84 坐标之间有数百米的偏差，谷歌不受此影响）拾取，或者更简单的，从[范例 4.1](https://altizure.github.io/sdk.examples/4-1-earth-pickpoint)拾取，点击想要的地方，就会显示了。console 里有日志, 要离地面近一点也能看到文字标签。
如果您也在使用 Altizure [GraphQL API](api.md), project 函数中有存地理位置的参数。在 geoInfo 里的 centerLat, centerLng， 可以分别赋值给 lat, lng。高度您可以自己定义，比如 1000(米)。

#### 2.20 平台支持 BIM 格式的文件吗？

目前不支持，有指定需要再开发。

#### 2.21 有没有方法修改 marker 的 position 坐标？同一个 marker，不销毁重建的情况下，如何修改其位置/通过鼠标点击地图来设置 marker 的位置？

```
marker.setPose (position, orientation, scale)
```

这里 `position: {lng, lat, alt}`, `orientation: {x, y, z, w}`, `scale: number`。
如果只改 `position`，另外两个可以设为 `undefined`，如 `setPose(position, undefined, undefined)`。

#### 2.22 将 OBJ 转成类似 Altizure 原生项目那样的金字塔分层（LOD）数据，有什么要求？

obj 需要符合一些数学规则，才可以做 LOD。包括：OBJ 只有三角形面。OBJ 模型里没有非2-流形的点和边，并且没有面积为0的面。详情需联系技术人员测试。

#### 2.23 如何给一个已有的 `PolyLineMarker` 添加更多的点？

使用 `altizure.PolyLineMarker::addPointMarker` 如：

```
polylineMarker.addPointMarker(new altizure.LngLatAlt(
  lng, lat, alt
))
```

#### 2.24 实景模型/OBJ 模型加载至地球指定位置，指定朝向，指定比例尺？
实景模型/OBJ 模型 使用的是同样的接口

marker.position 位置

marker.euler 欧拉方向

marker.orientation 四元数方向

marker.scale 比例尺

* [参考範例](https://altizure.github.io/sdk.examples/examples.sdk.html) 2.1， 2.5.1 
* [参考文档](https://altizure.github.io/dev-docs-site/zh-hans/docs/user_docs/web/Marker.html)

#### 2.25 制作OBJ模型的一些建议(非必须)
* 使用建模软件时，用 1m 作为单位长度，导入 SDK 时就不需要重新调整 scale 比例尺。
* （如果是对实景建筑物的建模）OBJ 模型和实景模型的形状需要尽量相近。
* 使用尽量少的面片数可以提升加载效率。
* 可将光照效果烘焙至纹理中，可以有更真实的渲染效果。
* 贴图尺寸采用 2^N 作为边长，比如 64&times;64, 128&times;256。

## 3. 关于坐标
#### 3.1 我们使用什么坐标？
本平台所使用的坐标系为 WGS84，通常谷歌、GPS（包括北斗）所采集到的经纬度均是在这一坐标系下的。

#### 3.2 WGS84 坐标、GCJ02 坐标、BD09 坐标
如果您在使用过程中不涉及国内地图厂商的产品的话，那您不需要花费过多的时间阅读本条目。

* WGS84 坐标即各种国际地图厂商和通用 GPS 设备所采用的坐标系。
* GCJ02 坐标（又称国测局坐标、火星坐标）是在 WGS84 基础上对大中华地区（有的地图产品不包含台湾）所有坐标进行加密，如果把采用 WGS84 坐标的物体放到 GCJ02 底图上，会产生100-700米不等的偏差。
* BD09 坐标是百度地图在 GCJ02 坐标的基础上再次加密所获得的坐标。

最简单的解决办法就是不论模型或底图均使用同一坐标系，如果使用了不同的坐标系，则需要进行坐标转换。

请注意：

国内主流地图厂商均提供了 API 方便用户从 WGS84 进行加密，但是反向解密（即将其他坐标系转为 WGS84）没有任何官方的解算方法，非官方解算方法均有误差且在大规模使用时**有可能触犯法律，请遵守当地法律法规**。Altizure 不提供任何可以反向解算坐标的接口。


#### 3.3 坐标相关资料

* [百度地图相关解释及转为BD09的API](http://lbsyun.baidu.com/index.php?title=coordinate)
* [高德地图相关解释及坐标转换API](https://lbs.amap.com/faq/top/coordinate/7) 
* [维基百科相关内容](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%8D%8E%E4%BA%BA%E6%B0%91%E5%85%B1%E5%92%8C%E5%9B%BD%E6%B5%8B%E7%BB%98%E9%99%90%E5%88%B6) 


## 4. 阅读更多

* [基础范例列表](https://altizure.github.io/sdk.examples/examples.sdk.html)
* [演示应用](jssdk-demo.md) 中展示了更加复杂的应用范例
* [Altizure JavaScript 3D SDK 详细文档](ref://docs/user_docs/web/)

—

该文档最后修改于 {{ file.mtime }}
