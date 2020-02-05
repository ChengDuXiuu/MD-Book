```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <title>SplitView Basic</title>
    <meta charset="UTF-8">
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
    <script type="text/javascript" src="ht-needed/echarts.js"></script>
    <script type="text/javascript" src="../ht.js"></script>
    <script type="text/javascript" src="echar.js"></script>
    <script>

        function init(){
            option = {
                title: {
                    text: 'Graph 简单示例'
                },
                tooltip: {},
                animationDurationUpdate: 1500,
                animationEasingUpdate: 'quinticInOut',
                series: [
                    {
                        type: 'graph',
                        layout: 'none',
                        symbolSize: 50,
                        roam: true,
                        label: {
                            show: true
                        },
                        edgeSymbol: ['circle', 'arrow'],
                        edgeSymbolSize: [4, 10],
                        edgeLabel: {
                            fontSize: 20
                        },
                        data: [{
                            name: '节点1',
                            x: 300,
                            y: 300
                        }, {
                            name: '节点2',
                            x: 800,
                            y: 300
                        }, {
                            name: '节点3',
                            x: 550,
                            y: 100
                        }, {
                            name: '节点4',
                            x: 550,
                            y: 500
                        }],
                        // links: [],
                        links: [{
                            source: 0,
                            target: 1,
                            symbolSize: [5, 20],
                            label: {
                                show: true
                            },
                            lineStyle: {
                                width: 5,
                                curveness: 0.2
                            }
                        }, {
                            source: '节点2',
                            target: '节点1',
                            label: {
                                show: true
                            },
                            lineStyle: {
                                curveness: 0.2
                            }
                        }, {
                            source: '节点1',
                            target: '节点3'
                        }, {
                            source: '节点2',
                            target: '节点3'
                        }, {
                            source: '节点2',
                            target: '节点4'
                        }, {
                            source: '节点1',
                            target: '节点4'
                        }],
                        lineStyle: {
                            opacity: 0.9,
                            width: 2,
                            curveness: 0
                        }
                    }
                ]
            };
            dataModel = new ht.DataModel();
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

            //糅合echars
            topView = new ht.widget.SplitView(graphView);
            bottomView = new ht.widget.SplitView(new ht.Chart(option));
            mainView = new ht.widget.SplitView(topView, bottomView, 'v', -100);


            view = mainView.getView();
            view.className = 'main';
            document.body.appendChild(view);
            window.addEventListener('resize', function (e) {
                mainView.iv();
            }, false);

        }

        function createDiv(background){
            var div = document.createElement('div');
            div.style.position = 'absolute';
            div.style.background = background;
            div.style.border = '10px solid yellow';
            div.style.setProperty('box-sizing', 'border-box', null);
            return div;
        }

    </script>
</head>
<body onload="init();">
</body>
</html>
```

echar.js
```javascript
ht.Chart = function(option){
    var self = this,
        view = self._view = document.createElement('div');
    // view = self._view = document.getElementById("main");
    view.style.position = 'absolute';
    view.style.setProperty('box-sizing', 'border-box', null);
    self._option = option;
};
ht.Default.def('ht.Chart', Object, {
    ms_v: 1,
    ms_fire: 1,
    ms_ac: ['chart', 'option', 'isFirst'],
    validateImpl: function(){
        var self = this,
            chart = self._chart;
        if(!chart){
            chart = self._chart = echarts.init(self.getView());
            chart.setOption(self._option);
        }
        chart.resize();
    }
});
```
