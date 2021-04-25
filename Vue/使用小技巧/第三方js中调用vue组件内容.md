## 背景

*   在vue中使用了 HT 的事件监听函数。函数中需要做大量和HT组件相关的操作
*   函数中使用vue 实例下任何数据 都无效

```javascript
parentGV.addInteractorListener((e) => {//e 鼠标事件
    if (e.kind === 'doubleClickData') {
        if (null == e.data) {
            return;
        }
        if (e.data.a("dataType") == 'area') { //区域
            let areaId = parentGV.dm().getSelectionModel().getLastData()._id;
            let areaName = parentGV.dm().getSelectionModel().getLastData()._name;
            //todo 获取区域下子网数据  然后展开对应树菜单
            topoViewObj._getSubnet(areaId,areaName)
            category = 'subnet'
            this.defaultExpandedKeys = [areaId]  #如果这里直接使用 data（）{}中属性是不行的。这里的defaultExpandedKeys是计算属性，通过修改vuex来实现的。
        }
    }
})
```



## 实现方式

### 1、vuex 状态管理

1.  新建js文件

    ```javascript
    # browsingVueData.js
    export default {
      namespaced: true,
      state: {
        keys: [],  //状态管理对象--即要操作的对象
        name: ''
      },
      mutations: {   // 对操作值 操作的方法
        updatekeys (state, keys) {
          state.keys = keys
        },
        clear (state){
          state.keys = []
        }
      }
    }
    ```

2.  index.js引入 注册

    ```javascript
    import Vuex from 'vuex'
    import cloneDeep from 'lodash/cloneDeep'
    import common from './modules/common'
    import user from './modules/user'
    import browsingVueData from './modules/browsingVueData'
    
    Vue.use(Vuex)
    
    export default new Vuex.Store({
      modules: {
        common,
        user,
        browsingVueData
      },
      mutations: {
        // 重置vuex本地储存状态
        resetStore (state) {
          Object.keys(state).forEach((key) => {
            state[key] = cloneDeep(window.SITE_CONFIG['storeState'][key])
          })
        }
      },
      strict: process.env.NODE_ENV !== 'production'
    })
    ```

3.  vue组件中只用 

    ```vue
    //计算属性提供api接口
    computed: {
          defaultExpandedKeys: {
            get () {
              // return this.$store.state.browsingVueData.count
              return function () {
                return this.$store.state.browsingVueData.keys
              }
            },
            set (val) { //数组
              this.$store.commit('browsingVueData/updatekeys', val)
            }
          }
        },
    
    //获取以及操作
    <el-tree
             :data="treeDataList"
             :props="props"
             node-key="id"
             :default-expanded-keys=defaultExpandedKeys()
             ref="tree"
    />
    //操作
     this.defaultExpandedKeys = [areaId]
     this.defaultExpandedKeys = []
    ```

    

### 2、Window下挂vue方法

```vue
    mounted: function () {
      //window下挂vue   dom生成时操作
      window.setTreeNodeAllExpand=this.setTreeNodeAllExpand
    },

//对应methods 中方法
//设置全部展开和折叠。state参数为bool值
      setTreeNodeAllExpand(state){
        this.defaultExpandedKeys = []
        var nodes=this.$refs.tree.store.nodesMap
        for(var i in nodes){
          nodes[i].expanded=state;
        }
      },

//使用
setTreeNodeAllExpand(false)
```

