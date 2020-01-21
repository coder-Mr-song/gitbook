# 4.cesium道路渲染

## 1.道路流动特效

```
function roadGeoFlowLoader(viewer, url, imgUrl, width, speed) {
            var img = imgUrl;

            Cesium.GeoJsonDataSource.load(url).then(function (ds) {

                viewer.dataSources.add(ds);
                // cesium.loaders._dataSources[url] = ds;

                /*
                 流动纹理线
                 color 颜色
                 duration 持续时间 毫秒
                 */
                function PolylineTrailLinkMaterialProperty(color, duration) {
                    this._definitionChanged = new Cesium.Event();
                    this._color = undefined;
                    this._colorSubscription = undefined;
                    this.color = color;
                    this.duration = duration;
                    this._time = (new Date()).getTime();
                }

                Cesium.defineProperties(PolylineTrailLinkMaterialProperty.prototype, {
                    isConstant: {
                        get: function () {
                            return false;
                        }
                    },
                    definitionChanged: {
                        get: function () {
                            return this._definitionChanged;
                        }
                    },
                    color: Cesium.createPropertyDescriptor('color')
                });
                PolylineTrailLinkMaterialProperty.prototype.getType = function (time) {
                    return 'PolylineTrailLink';
                }
                PolylineTrailLinkMaterialProperty.prototype.getValue = function (time, result) {
                    if (!Cesium.defined(result)) {
                        result = {};
                    }
                    result.color = Cesium.Property.getValueOrClonedDefault(this._color, time, Cesium
                        .Color.BLACK, result.color);
                    result.image = img;
                    result.time = (((new Date()).getTime() - this._time) % this.duration) / this
                        .duration;
                    return result;
                }
                PolylineTrailLinkMaterialProperty.prototype.equals = function (other) {
                    return this === other
                }

                Cesium.PolylineTrailLinkMaterialProperty = PolylineTrailLinkMaterialProperty;
                Cesium.Material.PolylineTrailLinkType = 'PolylineTrailLink';
                Cesium.Material.PolylineTrailLinkImage = img;
                Cesium.Material.PolylineTrailLinkSource = "czm_material czm_getMaterial(czm_materialInput materialInput)\n\
                                                          {\n\
                                                               czm_material material = czm_getDefaultMaterial(materialInput);\n\
                                                               vec2 st = materialInput.st;\n\
                                                               vec4 colorImage = texture2D(image, vec2(fract(st.s - time), st.t));\n\
                                                               material.diffuse = colorImage.rgb;\n\
                                                               material.alpha = colorImage.a;\n\
                                                               return material;\n\
                                                           }";
                Cesium.Material._materialCache.addMaterial(Cesium.Material.PolylineTrailLinkType, {
                    fabric: {
                        type: Cesium.Material.PolylineTrailLinkType,
                        uniforms: {
                            // color: new Cesium.Color(1.0, 0.0, 0.0, 0.5),
                            image: img,
                            time: 0
                        },
                        source: Cesium.Material.PolylineTrailLinkSource
                    },
                    translucent: function (material) {
                        return true;
                    }
                });

                var entities = ds.entities.values,
                    polyline;

                for (var i = 0, l = entities.length; i < l; i++) {
                    polyline = entities[i].polyline;
                    polyline.width = width;
                    polyline.clampToGround = true;

                    polyline.material = new Cesium.PolylineTrailLinkMaterialProperty(Cesium.Color.WHITE, speed)
                }

                $.ajax({
                    type: "get",
                    url: "../../resource/fengtaiLine.json",
                    success: function (data) {
                        let wall = viewer.entities.add({
                            name: "动态立体墙",
                            wall: {
                                positions: Cesium.Cartesian3.fromDegreesArray([]),
                                maximumHeights: [],
                                minimumHeights: [],
                                material: new Cesium.PolylineTrailLinkMaterialProperty(
                                    Cesium.Color.ORANGE, 3000)
                            }
                        });

                        const coordinates = data.features[0].geometry.coordinates[0];
                        let sourceArr;
                        $.each(coordinates, function (index, source) {
                            sourceArr = Cesium.Cartesian3.fromDegreesArray(source);
                            wall.wall.positions._value.push(sourceArr[0]);
                            wall.wall.maximumHeights._value.push(600);
                            wall.wall.minimumHeights._value.push(0);
                            wall.wall.material = new Cesium
                                .PolylineTrailLinkMaterialProperty(Cesium.Color.ORANGE,
                                    3000);
                        })



                        // for(let i=0;i<data.features[0].geometry.coordinates.length;i++){
                        //     console.log(data.features[0].geometry.coordinates[i]);
                        // }
                        // console.log(data.features[0].geometry.coordinates[0]);
                    }
                });
                // var wall=viewer.entities.add({
                //     name: "动态立体墙",
                //     wall: {
                //         positions: Cesium.Cartesian3.fromDegreesArray([117.154815, 31.853495,
                //             117.181255, 31.854257, 117.182284, 31.848255, 117.184748, 31.840141,
                //             117.180557, 31.835556, 117.180023, 31.833741, 117.166846, 31.833737,
                //             117.155531, 31.833151, 117.154787, 31.835978, 117.151994, 31.839036,
                //             117.150691, 31.8416, 117.151215, 31.844734, 117.154457, 31.848152,
                //             117.154815, 31.853495
                //         ]),
                //         maximumHeights: [600, 600, 600, 600, 600, 600, 600, 600, 600, 600, 600, 600,
                //             600, 600
                //         ],
                //         minimumHeights: [43.9, 49.4, 38.7, 40, 54, 51, 66.7, 44.6, 41.2, 31.2, 50.1,
                //             53.8, 46.9, 43.9
                //         ],
                //         material: new Cesium.PolylineTrailLinkMaterialProperty(Cesium.Color.ORANGE,
                //             3000)
                //     }
                // })
                // console.log(wall);
            });
        }
```

读取geojson文件形成路径,调用polyline方法,然后将图片赋予polyline材质上,用glsl语言形成效果.

## 2.道路发光特效

```
  function roadGeoGlowLoader(viewer, url, color, glowPower, width) {
            Cesium.GeoJsonDataSource.load(url).then(function (ds) {
                viewer.dataSources.add(ds);
                // cesium.loaders._dataSources[url] = ds;
                var entities = ds.entities.values;

                for (var i = 0, l = entities.length; i < l; i++) {
                    polyline = entities[i].polyline;
                    polyline.width = width;
                    polyline.clampToGround = true;
                    polyline.material = new Cesium.PolylineGlowMaterialProperty({
                        glowPower: glowPower,
                        color: color.withAlpha(.9)
                    });

                }
            });
        }
```

导入geojson文件形成路径,通过改变polyline的材质发光设置:glowPower控制发光强度.