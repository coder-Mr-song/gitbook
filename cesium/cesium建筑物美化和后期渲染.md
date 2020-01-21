# 5.cesium建筑物美化和后期渲染

## 1.建筑物闪烁特效

```
		var i = 0.2,
            number = 1,
            polyline;

        var render = function () {
            if (number % 2 != 0) {
                if (i > 1) {
                    number++;
                } else {
                    tileset.luminanceAtZenith = i;
                    i += 0.005;

                    // Line.position._value

                }
            } else {
                if (i < 0.2) {
                    number++;
                } else {
                    tileset.luminanceAtZenith = i;
                    i -= 0.005;
                }
            }
            requestAnimationFrame(render);
        };
        render();
```

通过动态改变luminanceAtZenith建筑物亮度来形成闪烁效果,定义一个亮度区间,亮度默认为0.2,可以将区间定义为0.2~1,定义奇偶来使亮度在区间内增大减小.调用帧函数不停渲染改变亮度,帧数越高闪烁越快,越低越慢.

## 2.后期特效

```
var bloom = viewer.scene.postProcessStages.bloom;
        var viewModel = {
            show: true,
            glowOnly: false,
            contrast: 119,
            brightness: -0.3,
            delta: 0.9,
            sigma: 3.78,
            stepSize: 2.0
        };

        bloom.enabled = Boolean(viewModel.show);
        bloom.uniforms.glowOnly = Boolean(viewModel.glowOnly);
        bloom.uniforms.contrast = Number(viewModel.contrast);
        bloom.uniforms.brightness = Number(viewModel.brightness);
        bloom.uniforms.delta = Number(viewModel.delta);
        bloom.uniforms.sigma = Number(viewModel.sigma);
        bloom.uniforms.stepSize = Number(viewModel.stepSize);
```

 viewer.scene.postProcessStages.bloom用于设置最终渲染,可以增加发光效果.参数为(contrast,brightness,glowOnly,delta,sigma,stepSize).

contrast`是在[-255.0，255.0]范围内的标量值，并且会影响效果的收缩。默认值为`128.0

brightness`是一个标量值。输入的纹理RGB值将转换为色相，饱和度和亮度（HSB），然后将此值添加到亮度中。默认值为`-0.3.

glowOnly`是一个布尔值。当`true`为时，将仅显示发光效果。当`false`为时，光晕将添加到输入纹理中。默认值为`false.

delta,sigma和stepSize用于调整色调和图像模糊程度.

