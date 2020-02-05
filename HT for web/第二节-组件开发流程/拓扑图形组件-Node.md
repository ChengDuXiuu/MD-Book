```HTML
<!--    必须有，否则显示不出来-->
    <style>
        html, body {
            padding: 0px;
            margin: 0px;
        }
        .main {
            margin: 0px;
            padding: 0px;
            position: absolute;
            top: 0px;
            bottom: 0px;
            left: 0px;
            right: 0px;
        }
    </style>
    <script type="text/javascript" src="jquery.js"></script>
    <script type="text/javascript" src="../ht.js"></script>
    <script>
        function init(){
            //第一步：创建数据容器
            dataModel = new ht.DataModel();
            //第二步：初始化组件
            graphView = new ht.graph.GraphView(dataModel);//绑定dataModel

            window.addEventListener('resize', function (e) {
                graphView.invalidate();
            }, false);

            //第三步：细化组件下元素达到想要的效果
            ht.Default.setImage('mac', 'image/1.jpg');

            air11 = new ht.Node();
            air11.setName('11-inch MacBook Air');
            air11.setImage('mac');
            air11.setSize(80, 43);
            air11.setPosition(100, 70);
            dataModel.add(air11);

            air13 = new ht.Node();
            air13.setName('13-inch MacBook Air');
            air13.setImage('image/2.jpg');
            air13.setPosition(260, 70);
            air13.setRotation(Math.PI/2);
            dataModel.add(air13);

            air11.setHost(air13);//吸附

            graphView.setEditable(true);//拓扑中图元是否可编辑
            graphView.setRectEditableFunc(function(data){//设置大小编辑过滤器函数（添加大小编辑功能）
                return data === air11;
            });
            graphView.setRotationEditableFunc(function(data){//设置旋转编辑过滤器函数（添加旋转功能）
                return data === air13;
            });

            var eventType = ht.Default.isTouchable ? 'touchend' : 'mouseup';//判断是否为触屏可Touch方式交互，HT系统一般会自动判断，对于极少数HT无法正确识别的系统下，可以通过配置强制指定
            graphView.getView().addEventListener(eventType, function(e){
                var data = graphView.getDataAt(e);//传入逻辑坐标点或者交互event事件参数，返回当前点下的图元，filter可进行过滤
                if(data && ht.Default.isDoubleClick(e)){
                    alert(data.getName() + ' is double clicked.');
                }
            });

            //第四步：获取组件根层div放置到需要显示的地方
            view = graphView.getView();//获取拓扑组件根层div
            view.className = 'main';
            document.body.appendChild(view);

        }

        $(document).ready(function () {
            init();
        })
    </script>

```
