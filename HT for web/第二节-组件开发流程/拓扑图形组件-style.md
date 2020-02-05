HT的数据Data可分为三种属性类型：

1. get/set或is/set类型，例如getName()、setName('ht')和isExpaned()，用于常用属性操作

    * getIcon() → {String|Object}
    获取小图标名称

    * getId() → {Number}
    获取唯一编号

    * getLayer() → {String|Number}
    获取数据元素在GraphView组件中的图层位置

    * getName() → {String}
    获取数据元素名

    * getParent() → {ht.Data}
    获取父元素
2. attr类型，通过getAttr(name)和setAttr(key, value)存取，该类型是HT预留给用户存储业务数据
    * edge.setAttr("inflow", data.inflow[j]);
    * edge.setAttr("outflow", data.outflow[j]);
    * edge.setAttr("speed", data.speed[j]);
    * edge.setAttr("inplp", data.inplp[j]);
    * edge.setAttr("outplp", data.outplp[j]);
3. style类型，通过getStyle(name)和setStyle(name, value)进行操作，GraphView上图元样式由该类型属性控制
    * setStyle(name, value)
    设置样式

      * 以下为部分style属性说明，更多属性由后续章节介绍：

          * image.stretch绘制图片的拉伸类型，默认为fill，可设为uniform或centerUniform，参见image
        * ingroup决定图元是否要包含在展开的Group内部，默认为true
        * opacity属性用于控制整个图元的透明度，可取值为0~1
        * body.color设置该颜色将改变图元中心渲染，可重 载GraphView.getBodyColor(data)函数自定义：
          * 对于显示image图片的Node，将自动绘制成被body.color染色后的图片
          * 对于设置了shape的矢量，如需要填充背景，则shape.background被body.color替代
          * 对于设置了shape的矢量，如无需填充背景，则shape.border.color被body.color替代
          *


          对于Edge的连线类型，edge.color会被body.color替代
