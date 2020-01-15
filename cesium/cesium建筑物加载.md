# 2.cesium加载建筑物

## 一、加载3DTiles建筑物

```
var tileset = viewer.scene.primitives.add(new Cesium.Cesium3DTileset({
            url: '../../resource/Buildings/tileset.json',
            maximumScreenSpaceError: 32,
            luminanceAtZenith: 0.2,
            imageBasedLightingFactor: new Cesium.Cartesian2(0.5, 0.5),
            // lightColor: new Cesium.Cartesian3(10, 25, 0),
        }));
```

加载方法:new Cesium.Cesium3DTileset();

URL: '../resource/Buildings/tileset.json';

maximumScreenSpaceError	屏幕最大容错率,默认为16,值越大,加载越快,但位置偏移越大,值越小,加载越慢,偏移越小.

luminanceAtZenith	太阳光照度,默认为0.2,值越大,建筑越亮,值越小,建筑越暗.

imageBasedLightingFactor	漫反射和镜面照明度,默认为Cartesian2(1.0, 1.0),为0禁用光源.

lightColor	用于遮挡模型的阳光的颜色和强度,默认为undefined;增加值将会使建筑更亮.

## 二、建筑样式

```
tileset.style = new Cesium.Cesium3DTileStyle({
        color: {
            conditions: [
                ['${height} >= 300', 'rgba(45, 0, 75, 0.5)'],
                ['${height} >= 200', 'rgb(102, 71, 151)'],
                ['${height} >= 100', 'rgb(170, 162, 204)'],
                ['${height} >= 50', 'rgb(224, 226, 238)'],
                ['${height} >= 25', 'rgb(252, 230, 200)'],
                ['${height} >= 10', 'rgb(248, 176, 87)'],
                ['${height} >= 5', 'rgb(198, 106, 11)'],
                ['true', 'rgb(127, 59, 8)']
            ]
        }
    });
```

加载方法:new Cesium.Cesium3DTileStyle

主要改变建筑物颜色,可以根据各种条件改变:如根据建筑物高度设置不同颜色;根据经纬度和根据距离视角远近设置不同颜色.

## 三、制作3DTiles格式文件

使用工具cesiumLab制作3DTiles文件,准备好建筑shp源文件.

![image-20200115144445410](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200115144445410.png)

![image-20200115144620037](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200115144620037.png)

![image-20200115144700408](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200115144700408.png)

![image-20200115144822065](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200115144822065.png)

可以根据条件添加多个纹理图片

点击确认,开始转换,图片质量越高,占用内存越大.