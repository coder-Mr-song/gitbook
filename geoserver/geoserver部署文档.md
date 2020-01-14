# geoserver部署流程

## 一、部署geoserver

### 		1.准备geoserver.war文件,放入tomcat目录下

![image-20200114112024927](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114112024927.png)

### 		2.如果访问地图跨域,需要先解决跨域问题

![image-20200114114142776](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114114142776.png)

或者直接解决tomcat跨域问题

首先下载cors-filter-1.7.jar，Java-property-utils-1.9.jar包放入目录下		

![image-20200114114251035](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114114251035.png)

修改web.xml文件

![image-20200114114742591](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114114742591.png)

添加如下内容:

```
<filter>
       <filter-name>CORS</filter-name>
       <filter-class>com.thetransactioncompany.cors.CORSFilter</filter-class>
       <init-param>
        <param-name>cors.allowOrigin</param-name>
           <param-value>*</param-value>
       </init-param>
       <init-param>
        <param-name>cors.supportedMethods</param-name>
           <param-value>GET, POST, HEAD, PUT, DELETE</param-value>
       </init-param>
       <init-param>
        <param-name>cors.supportedHeaders</param-name>
           <param-value>Accept, Origin, X-Requested-With, Content-Type, Last-Modified</param-value>
       </init-param>
       <init-param>
           <param-name>cors.exposedHeaders</param-name>
           <param-value>Set-Cookie</param-value>
       </init-param>
       <init-param>
           <param-name>cors.supportsCredentials</param-name>
           <param-value>true</param-value>
       </init-param>
   </filter>
 
   <filter-mapping>
       <filter-name>CORS</filter-name>
       <url-pattern>/*</url-pattern>
   </filter-mapping>
```

3.重启tomcat,访问http://10.168.80.218:8080/geoserver (IP:8080/geoserver)

![image-20200114130621971](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114130621971.png)

## 二、发布服务

### 1.将shp源文件放入指定文件夹内

![image-20200114131026145](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131026145.png)

### 2.设计地图样式并导出sld样式文件(使用QGIS或udig)

可以新建项目或者导入已经设计好的qgz项目文件

![image-20200114131250871](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131250871.png)

![image-20200114131313244](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131313244.png)

![image-20200114131659985](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131659985.png)

导入shp文件时要注意文件编码,默认system,尽量不要改为utf-8,修改后geoserver可能会显示文字乱码.

打开属性设置相应的样式

![image-20200114131515674](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131515674.png)

![image-20200114131541633](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131541633.png)

可以导出shp或其他文件

![image-20200114131931358](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114131931358.png)

设置好样式后导出sld样式文件

![image-20200114132007387](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132007387.png)

![image-20200114132038343](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132038343.png)

### 3.新建工作区

![image-20200114132156646](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132156646.png)

### 4.新建数据存储

![image-20200114132504379](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132504379.png)

![image-20200114132617845](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132617845.png)

![image-20200114132728287](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132728287.png)

选取刚才新建的工作区,选取shp所在的文件夹

### 5.新建地图样式

![image-20200114132914970](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114132914970.png)

![image-20200114133107773](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133107773.png)

输入样式名称,选择工作区,选择对应的sld文件,点击upload

![image-20200114133237790](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133237790.png)

样式上传后会显示在下方,无误后点击提交

### 6.新建图层

![image-20200114133419056](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133419056.png)

![image-20200114133438623](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133438623.png)

添加图层选中后,会出现该文件夹下的shp文件,可以看到是否发布,点击发布按钮

![image-20200114133731436](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133731436.png)

![image-20200114133826710](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133826710.png)点击自动生成边界位置和范围

![image-20200114133944931](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114133944931.png)

如果修改了相应的图层文件,需要点击重新载入要素

![image-20200114134052239](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114134052239.png)

点击发布选项,指定对应的style样式

![image-20200114134158320](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114134158320.png)

在缓存选项,可以修改发布的瓦片类型(geojson等矢量瓦片需要下载对应的jar包才有缓存选项)可以指定发布的坐标系类型(EPSG:4326即经纬度坐标,EPSG:900913即谷歌墨卡托投影坐标),确认无误后点击保存

样式和图层是配合使用的,有多少shp源文件就需要发布多少图层,图层对应style样式文件即可.



### 7.新建图层组

![image-20200114134855860](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114134855860.png)

![image-20200114135048752](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135048752.png)

![image-20200114135206692](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135206692.png)选中工作区后,添加相应的图层,点击生成边界按钮自动生成.

![image-20200114135243735](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135243735.png)

选择可以发布的格式和坐标,点击保存

### 8.查看地图并缓存切片

![image-20200114135408581](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135408581.png)

![image-20200114135426495](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135426495.png)

![image-20200114135441918](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135441918.png)

如果出现,说明发布成功,如果一片空白,说明发布的图层中有数据损坏或错误的,需要找到错误的图层并改正(一般空白都是shp源文件引起的,可以在QGIS中转换成shp文件再进行导入)

![image-20200114135710435](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114135710435.png)

![image-20200114140031797](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114140031797.png)



可以修改缓存目录方便找到

![image-20200114140308804](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114140308804.png)

![image-20200114140503721](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114140503721.png)

geoserver导入SVG图像

![image-20200114140749517](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114140749517.png)

需要先将SVG图像放入指定文件夹内才可以识别

![image-20200114140911833](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200114140911833.png)

需要将导入的sld文件中的路径更改为图标和名称,因为放在工作空间内所以不需要添加路径,直接识别.