# protobuf 3.5 java使用介绍（二）

protobuf 3.5 java使用介绍（二）

上一篇遗留了两个问题：

1，数据模型中有可能会出现数组格式，而数组里面是一个其他的模型，这个怎么来做？

2，构建数据消息的时候，通常会有一个头，一个体；根据头中定义的数据类型不同，体里面的数据模型也不相同，这个又该怎么处理？


**模型数组**


假如我们需要一个"路径"的模型，路径由很多个"点"组成，同时在路径中还有一些其它的属性信息，其中类型为定义好的几个值。

1，首先，定义一个"pint"：

```
syntax = "proto3";

package iot.adsb.vip;

message point{
    double lon = 2; //经度
    double lat = 3; //纬度
}
```

2，然后，定义"path"

```
syntax = "proto3";

import "point.proto";

package iot.adsb.vip;

message path
{
    enum typeEnum {
        COMMUTE = 0;  //通勤
        DUTY = 1;     //工作
    }
    string id = 1;   
    string name = 2; //名称
    typeEnum type = 3; //类型
    repeated point geometry = 4; //点集合
}
```

好了，这样两个protobuf的文件就满足了我们的要求，生成一下代码看看。

3，进入proto文件所在的文件夹，执行

	protoc --java_out=. *.proto

然后可以在当前文件夹下看到生成好的java代码

4，下面，我们来使用一下生成好的模型测试一下

```
Point.point.Builder point_builder1 = Point.point.newBuilder();
        point_builder1.setLat(101.11);
        point_builder1.setLon(39.11);
        Point.point point1 = point_builder1.build();
        System.out.println(point1.toString());
        System.out.println("===== 构建point1模型结束 =====");

        Point.point.Builder point_builder2 = Point.point.newBuilder();
        point_builder2.setLat(101.11);
        point_builder2.setLon(39.11);
        Point.point point2 = point_builder2.build();
        System.out.println(point1.toString());
        System.out.println("===== 构建point2模型结束 =====");
    
        Path.path.Builder pathBuilder = Path.path.newBuilder();
        pathBuilder.setId("1");
        pathBuilder.setName("路径");
        pathBuilder.setType(Path.path.typeEnum.DUTY);
    
        pathBuilder.addGeometry(0, point1);
        pathBuilder.addGeometry(1, point2);
        Path.path path = pathBuilder.build();
        System.out.println(path.toString());
        System.out.println("===== 构建path模型结束 =====");
    
        System.out.println("===== 转成json对象开始 =====");
        String jsonFormatM = "";
        try {
            jsonFormatM = JsonFormat.printer().print(path);
        } catch (Exception e) {
            e.printStackTrace();
        }
    
        System.out.println(jsonFormatM.toString());
        System.out.println("json数据大小：" + jsonFormatM.getBytes().length);
        System.out.println("===== 转成json对象结束 =====");

```

结果如下：

```
Connected to the target VM, address: '127.0.0.1:52895', transport: 'socket'
lon: 39.11
lat: 101.11

===== 构建point1模型结束 =====
lon: 39.11
lat: 101.11

===== 构建point2模型结束 =====
id: "1"
name: "\350\267\257\345\276\204"
type: DUTY
geometry {
  lon: 39.11
  lat: 101.11
}
geometry {
  lon: 39.11
  lat: 101.11
}

===== 构建path模型结束 =====
===== 转成json对象开始 =====
{
  "id": "1",
  "name": "路径",
  "type": "DUTY",
  "geometry": [{
    "lon": 39.11,
    "lat": 101.11
  }, {
    "lon": 39.11,
    "lat": 101.11
  }]
}
json数据大小：155
===== 转成json对象结束 =====
Disconnected from the target VM, address: '127.0.0.1:52895', transport: 'socket'

Process finished with exit code 0

```

**消息头/体结构**

通常在做实际项目的时候，消息是需要用信封做一下封装便于业务的处理和一些业务无关的信息（例如：ID，加密信息等）存放。消息体根据消息类型是不同的数据类型。

实现这个的方式其实很简单，protobuf对象可以序列化成byte数组，然后，在信封的模型里定义一个 bytes 类型的字段用于存放消息体即可。

1，如下定义消息信封：

```
syntax = "proto3";

import "point.proto";

package iot.adsb.vip;

message msg
{
  string mid = 1;
  string type = 2;
  bytes data = 3; //数据内容
}
```

2，按照上面的方法，生成JAVA模型

3，测试一下

```
public static void main(String[] args) {
        // 消息体为 Point
        Point.point.Builder point_builder1 = Point.point.newBuilder();
        point_builder1.setLat(101.11);
        point_builder1.setLon(39.11);
        Point.point point1 = point_builder1.build();
        System.out.println(point1.toString());
        System.out.println("===== 构建point1模型结束 =====");

        // 构建消息
        Msg.msg.Builder msgBuilder = Msg.msg.newBuilder();
        msgBuilder.setMid("1");
        msgBuilder.setType("PATH");
        msgBuilder.setData(point1.toByteString());
    
        Msg.msg msg = msgBuilder.build();
        System.out.println(msg.toString());
        System.out.println("===== 构建msg模型结束 =====");
    
        byte[] msgBytes = msg.toByteArray();
    
        // 解析消息
        try {
            Msg.msg msgParse = Msg.msg.parseFrom(msgBytes);
            System.out.println(msgParse.toString());
            System.out.println("===== 反序列化msg模型结束 =====");
    
            System.out.println("PATH取值");
            System.out.println(msgParse.getMid());
            System.out.println(msgParse.getType());
    
            switch (msgParse.getType()) {
                case "PATH":
                    System.out.println("PATH消息");
                    Point.point pointParse = Point.point.parseFrom(msgParse.getData());
                    System.out.println(pointParse.toString());
                    System.out.println("===== 反序列化point模型结束 =====");
                    System.out.println("Point取值");
                    System.out.println("纬度:" + pointParse.getLat());
                    System.out.println("经度:" + pointParse.getLon());
                    break;
                case "":
                    break;
            }
    
        } catch (InvalidProtocolBufferException ex) {
            System.out.println(ex);
        }
    }
```

测试结果

```
Connected to the target VM, address: '127.0.0.1:49427', transport: 'socket'
lon: 39.11
lat: 101.11

===== 构建point1模型结束 =====
mid: "1"
type: "PATH"
data: "\021\256G\341z\024\216C@\031\327\243p=\nGY@"

===== 构建msg模型结束 =====
mid: "1"
type: "PATH"
data: "\021\256G\341z\024\216C@\031\327\243p=\nGY@"

===== 反序列化msg模型结束 =====
PATH取值
1
PATH
PATH消息
lon: 39.11
lat: 101.11

===== 反序列化point模型结束 =====
Point取值
纬度:101.11
经度:39.11
Disconnected from the target VM, address: '127.0.0.1:49427', transport: 'socket'

Process finished with exit code 0
```

[代码下载地址](https://download.csdn.net/download/fangxiaoji/15333028)

[上一篇](https://blog.csdn.net/fangxiaoji/article/details/78826165)

————————————————

版权声明：本文为CSDN博主「全栈仁波切」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：https://blog.csdn.net/fangxiaoji/article/details/113834815
