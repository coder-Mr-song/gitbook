# 3.cesium加载模型

```
  var position = coordinateSystem.cartesianToPosition(viewer, position),
                    modelMatrix,
                    pm = Cesium.Transforms.eastNorthUpToFixedFrame(
                        Cesium.Cartesian3.fromDegrees(position[0], position[1], position[2]));
                if (attitude) {
                    var rx = Cesium.Math.toDegrees(attitude.pitch),
                        ry = Cesium.Math.toDegrees(attitude.heading),
                        rz = Cesium.Math.toDegrees(attitude.roll);

                    var rxm = Cesium.Matrix4.multiplyByMatrix3(pm, Cesium.Matrix3.fromRotationX(Cesium.Math.toRadians(rx)), pm),
                        rym = Cesium.Matrix4.multiplyByMatrix3(rxm, Cesium.Matrix3.fromRotationY(Cesium.Math.toRadians(ry)), rxm),
                        rzm = Cesium.Matrix4.multiplyByMatrix3(rym, Cesium.Matrix3.fromRotationZ(Cesium.Math.toRadians(rz)), rym);
                    modelMatrix = rzm;
                } else {
                    modelMatrix = pm;
                }


                var model = viewer.scene.primitives.add(Cesium.Model.fromGltf({
                    url: url, //如果为bgltf则为.bgltf
                    modelMatrix: modelMatrix,
                    scale: scale //放大倍数
                }));
```

加载方法:Cesium.Model.fromGltf().

URL:gltf模型文件或者glb模型文件.

modelMatrix:模型摆放位置及朝向,需要用Cesium.Cartesian3.fromDegrees先将经纬度转换为Cartesian3格式,通过eastNorthUpToFixedFrame方法将Cartesian3格式的数据转换并设置好方向为Matrix4格式.

scale:模型放大倍数.