# 1.cesium加载地图

![image-20200115132330872](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200115132330872.png)

这是所有的地图加载方式,只简单介绍几个常用到的加载方法.

## 一、加载arcgis地图

```
 imageryProvider: new Cesium.ArcGisMapServerImageryProvider({
                url: 'http://10.168.80.218:6080/arcgis/rest/services/fengtaiMap/MapServer',
                hasAlphaChannel: false
            })
```

加载方法: new Cesium.ArcGisMapServerImageryProvider();

URL:http://10.168.80.218:6080/arcgis/rest/services/fengtaiMap/MapServer (这是常见的访问格式,用于访问坐标系是EPSG:3857的地图瓦片,不需要任何更改就可以访问到)

URL:http://10.168.80.181:6080/arcgis/rest/services/fengtaiMap/MapServer/WMTS?request=GetTile (这是访问EPSG:4326坐标系的访问格式)

## 二、加载geoserver地图

```
new Cesium.WebMapServiceImageryProvider({
                url: 'http://10.168.80.218:8080/geoserver/gwc/service/wms',
                layers: 'fengtaiMap:fengtaiMap', // GeoServer指定的图层,
                enablePickFeatures: false,
                parameters: {
                    service: 'WMS',
                    format: 'image/png',
                    transparent: true
                }
            })
```

加载方法:new Cesium.WebMapServiceImageryProvider();

URL:http://10.168.80.218:8080/geoserver/gwc/service/wms (采用WMS加载方式,需要注意要指定layers图层和瓦片格式为PNG,不然默认加载jpeg格式)

geoserver加载地图分为两种:http://10.168.80.218:8080/geoserver/wms

​												http://10.168.80.218:8080/geoserver/gwc/service/wms

第一种加载实时渲染地图,速度较慢,而且不会缓存瓦片

第二种会先去加载缓存瓦片,如果没有瓦片就实时渲染并缓存下来,这种在有缓存瓦片的情况下速度比较快

## 三、加载在线地图

```
 new Cesium.UrlTemplateImageryProvider({
                url: "http://mt1.google.cn/vt/lyrs=s&hl=zh-CN&x={x}&y={y}&z={z}&s=Gali"
            })
```

加载方法:new Cesium.UrlTemplateImageryProvider();

URL:http://mt1.google.cn/vt/lyrs=s&hl=zh-CN&x={x}&y={y}&z={z}&s=Gali (这是谷歌地图,只要指定在线地图提供的url即可)



cesium可以加载多个图层堆叠到一起,可以修改图层的透明度,亮度,对比度和伽马值等等.

```
 let layer0 = viewer.scene.imageryLayers.get(0);
 		layer0.alpha = 1;
        layer0.brightness = 0.8;
        layer0.saturation=0.8;
        layer0.contrast = 1
```

viewer.scene.imageryLayers.get(1)	//获取地图图层,从0开始,这里是获取第一个图层.

viewer.scene.globe.maximumScreenSpaceError	//地图瓦片最大空间容错率,默认为2,值越大,瓦片越粗糙,加载越快;值越小,瓦片越精细,加载越慢.

 viewer.scene.globe.tileCacheSize	//地图缓存容量,默认为512MB,值越大,缓存瓦片越多,占用内存越多.

viewer.scene.globe.preloadSiblings	//预加载同级相邻的瓦片,为true可以获得更好的平移体验,但需要加载更多瓦片.

viewer.scene.globe.preloadAncestors	//预加载瓦片的父级或自己,可以获得更好的缩放体验,但需要加载更多瓦片.