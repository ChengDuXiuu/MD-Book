## 标题栏跳转实现

1. 标题栏独立成一个组件

	```vue
	<template>
	<div>
	  <router-link  :to="{name:'Home'}">
	    <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">首页</span>
	  </router-link>
	  <router-link  :to="{name:'BlogFile'}">
	    <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">博客归档</span>
	  </router-link>
	  <router-link  :to="{name:'ProjectFile'}">
	    <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">Git归档</span>
	  </router-link>
	  <router-link  :to="{name:'Home'}">
	    <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">分类</span>
	  </router-link>
	  <router-link  :to="{name:'Home'}">
	    <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">标签</span>
	  </router-link>
	</div>
	</template>
	
	<script>
	    export default {
	        name: "Title"
	    }
	</script>
	
	<style scoped>
	
	</style>
	```

2. Home.vue组件中引入并替换

	```vue
	import Title from '../components/Title.vue'
	components: {BlogList,Title},
	<Title ref="blogTitle"></Title>
	```

3. 新建BlogFile组件和ProjectFile组件

	```vue
	# BlogFile.vue
	<template>
	  <div>
	    <el-container style="">
	      <el-header style="background: #333333;">
	        <el-row :gutter="24" style="height: 50px;">
	          <el-col :span="4"><div class="grid-content bg-purple" ></div></el-col>
	          <el-col :span="14"><div class="grid-content bg-purple" >
	            <Title></Title>
	          </div></el-col>
	          <el-col :span="6"><div class="grid-content bg-purple" ></div></el-col>
	        </el-row>
	      </el-header>
	      <el-main>
	        <el-row type="flex" style="margin-top: 20px;">
	          <el-col :span="4">
	
	          </el-col>
	          <el-col :span="16">
	            <el-alert
	              title="有些的时候，正是为了爱才悄悄躲开。躲开的是身影，躲不开的却是那份默默的情怀。"
	              type="success"
	              :closable="false"
	              center>
	            </el-alert>
	          </el-col>
	          <el-col :span="4" >
	
	          </el-col>
	        </el-row>
	        <el-divider style="height: 3px" />
	        <el-row type="flex">
	          <el-col :span="4">
	
	          </el-col>
	          <el-col :span="4">
	            <a-timeline>
	              <a-timeline-item v-for="time in yearMonth" @click="selectByMonth(time)" :color="randomColor()" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'" style="margin-bottom: 15px">
	                {{ time.time }}
	              </a-timeline-item>
	            </a-timeline>
	          </el-col>
	          <el-col :span="12">
	            <a-timeline>
	              <a-timeline-item :color="randomColor()" v-for="article in articles">
	                <el-card>
	                  <router-link target="_blank" :to="{name:'BlogDetail',params:{blogId:article.id}}">
	                    <a onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'" style="font-size: 150%;">
	                      {{ article.title}}
	                    </a>
	                  </router-link>
	                  <br/>
	                  <br/>
	                  <a-tag :color="randomColor()" v-for="tag in getTagByArticle(article.tag)">
	                    {{ tag }}
	                  </a-tag>
	                </el-card>
	              </a-timeline-item>
	            </a-timeline>
	          </el-col>
	          <el-col :span="4">
	
	          </el-col>
	        </el-row>
	      </el-main>
	    </el-container>
	  </div>
	</template>
	
	<script>
	import Title from '../components/Title.vue'
	import {
	  getAction,
	  postAction,
	} from '../api/manage'
	
	const fontColorMap = new Map([//DarkVoilet
	  [0, 'f7acbc'],
	  [1, 'ef5b9c'],
	  [2, 'f05b72'],
	  [3, 'f8aba6'],
	  [4, 'f58f98'],
	  [5, 'bd6758'],
	  [6, '660066'],
	  [7, '660000'],
	  [8, '6600FF'],
	  [9, '666633'],
	  [10, '990066'],
	  [11, '9900FF'],
	  [12, '996633'],
	  [13, '990000'],
	  [14, '9966FF'],
	  [15, '9999CC'],
	  [16, 'FF3333'],
	  [17, 'FF6699'],
	  [18, 'FF66FF'],
	  [19, 'FFFF99'],
	  [20, '680000'],
	  [21, '686868'],
	  [22, '7fb80e'],
	
	  [23, 'bed742'],
	  [24, 'b7ba6b'],
	  [25, '769149'],
	  [26, '6d8346'],
	  [27, 'cde6c7'],
	  [28, '1d953f'],
	  [29, '77ac98'],
	  [30, '007d65'],
	  [31, '45b97c'],
	  [32, '225a1f'],
	  [33, '2b6447'],
	  [34, '005831'],
	  [35, '006c54'],
	  [36, '274d3d'],
	  [37, '65c294'],
	  [38, '00ae9d'],
	  [39, '90d7ec'],
	  [40, '2468a2'],
	  [41, '1b315e'],
	  [42, 'decb00'],
	  [43, '596032'],
	  [44, '5f5d46'],
	  [45, 'ffd400'],
	  [46, 'b69968'],
	  [47, '6d5826'],
	  [48, 'ba8448'],
	  [49, 'e0861a'],
	  [50, 'c88400'],
	  [51, 'fab27b'],
	  [52, 'f47920'],
	  [53, 'fedcbd'],
	  [54, 'f36c21'],
	  [55, 'b7704f'],
	  [56, 'c99979'],
	  [57, '444693'],
	  [58, '2b4490'],
	  [59, '224b8f'],
	  [60, '46485f'],
	  [61, '6a6da9'],
	  [62, '494e8f'],
	  [63, '9b95c9'],
	  [64, '6950a1'],
	  [65, '594c6d'],
	  [66, '6f599c'],
	  [67, '401c44'],
	  [68, 'c77eb5'],
	  [69, 'f173ac'],
	  [70, '4f5555'],
	  [71, '563624'],
	  [72, '6c4c49'],
	  [73, '3c3645'],
	]);
	export default {
	  name: 'BlogFile',
	  components: {
	    Title,
	  },
	  data() {
	    return {
	      description: '博客归档',
	      articles: [],
	      yearMonth: [],
	      url: {
	        groupByMonth: '/article/blogArticle/groupByMonth',
	        seleteByMonth: '/article/blogArticle/seleteByMonth',
	      },
	      articleView :{
	        id: '',
	        title: '',
	        visible: false,
	        fullscreen: true,
	        switchFullscreen: true,
	        htmlContent: '',
	      },
	    }
	  },
	  computed: {
	    getTagByArticle: function () {
	      return function (tag) {
	        return tag.split(",");
	      }
	    },
	  },
	  created() {
	    this.groupByMonth();
	  },
	  methods: {
	     groupByMonth(param){
	       getAction(this.url.groupByMonth, param)
	         .then((res) => {
	           console.log("根据月份获取文章》》",res)
	           if (res.data.success) {
	             res.data.result.forEach((item, i) => {
	               this.yearMonth.push({
	                 time: item.yearTime,
	               })
	             })
	             console.log("分组：",this.yearMonth);
	             this.selectByMonth(this.yearMonth[0]);
	           }
	         })
	         .catch((e) => {
	           this.$message({
	             message: '根据月份获取文章失败 !',
	             type: 'warning'
	           });
	           console.log('根据月份获取文章失败', e)
	         })
	     },
	     selectByMonth(yearMonth){
	       this.articles=[];
	       let param ={
	         createTime : yearMonth.time,
	       }
	       postAction(this.url.seleteByMonth,param).then(res=>{
	         console.log("获取该月份文章",yearMonth.time,res);
	         if(res.data.success){
	           res.data.result.forEach((item, i) => {
	             this.articles.push({
	               id: item.id,
	               title: item.title,
	               description: item.htmlContent.substring(0, 50),
	               tag: item.tag,
	               htmlContent: item.htmlContent,
	               mdContent: item.mdContent,
	               collection: item.numCollections,
	               star: item.numLikes,
	               message: item.numComments,
	             })
	           })
	         }else{
	           this.$message({
	             message: '获取该月份文章失败 !',
	             type: 'warning'
	           });
	           console.log('获取该月份文章失败', e)
	         }
	       })
	     },
	     randomColor() {
	        return "#"+fontColorMap.get(Math.round(Math.random()*73));
	      },
	  }
	}
	</script>
	<style scoped>
	  @import '../assets/Home.css';
	
	</style>
	```

	```vue
	#ProjectFile.vue
	<template>
	  <div>
	    <el-container style="">
	      <el-header style="background: #333333;">
	        <el-row :gutter="24" style="height: 50px;">
	          <el-col :span="4"><div class="grid-content bg-purple" ></div></el-col>
	          <el-col :span="14"><div class="grid-content bg-purple" >
	            <Title ref="blogTitle"></Title>
	          </div></el-col>
	          <el-col :span="6"><div class="grid-content bg-purple" ></div></el-col>
	        </el-row>
	      </el-header>
	      <el-main>
	        <el-row type="flex">
	          <el-col :span="4"></el-col>
	          <el-col :span="16">
	            <el-alert
	              title="有些的时候，正是为了爱才悄悄躲开。躲开的是身影，躲不开的却是那份默默的情怀。"
	              type="success"
	              :closable="false"
	              center>
	            </el-alert>
	          </el-col>
	          <el-col :span="4" ></el-col>
	        </el-row>
	        <el-divider style="height: 3px" />
	        <el-row type="flex">
	          <el-col :span="4"></el-col>
	          <el-col :span="16">
	            <el-row>
	              <el-timeline>
	                <el-timeline-item
	                  v-for="(projectFile, index) in projectFiles"
	                  :key="index"
	                  :icon="projectFile.icon"
	                  :type="projectFile.type"
	                  :color="projectFile.color"
	                  size="large"
	                  :timestamp="projectFile.date">
	                  <el-card>
	                    <h4>{{ projectFile.commitMsg }}</h4>
	                    <el-divider style="height: 3px" />
	                    <p><font :color="randomFontColor()">{{ projectFile.name }}</font>  提交于  {{ projectFile.date }}</p>
	                  </el-card>
	                </el-timeline-item>
	              </el-timeline>
	            </el-row>
	          </el-col>
	          <el-col :span="4"></el-col>
	        </el-row>
	      </el-main>
	    </el-container>
	  </div>
	</template>
	<script>
	import Title from '../components/Title.vue'
	import {
	  getAction,
	  postAction,
	} from '../api/manage'
	const colorMap = new Map([
	  [0, 'primary'],
	  [1, 'success'],
	  [2, 'info'],
	  [3, 'warning'],
	  [4, 'danger'],
	]);
	const iconMap = new Map([
	  [0, 'el-icon-star-on'],
	  [1, 'el-icon-user-solid'],
	  [2, 'el-icon-platform-eleme'],
	  [3, 'el-icon-s-tools'],
	  [4, 'el-icon-s-goods'],
	  [5, 'el-icon-phone'],
	  [6, 'el-icon-success'],
	  [7, 'el-icon-circle-plus'],
	  [8, 'el-icon-remove'],
	  [9, 'el-icon-error'],
	  [10, 'el-icon-s-help'],
	  [11, 'el-icon-upload'],
	  [12, 'el-icon-picture'],
	  [13, 'el-icon-message-solid'],
	  [14, 'el-icon-s-platform'],
	  [15, 'el-icon-s-opportunity'],
	  [16, 'el-icon-dessert'],
	]);
	const fontColorMap = new Map([//DarkVoilet
	  [0, 'f7acbc'],
	  [1, 'ef5b9c'],
	  [2, 'f05b72'],
	  [3, 'f8aba6'],
	  [4, 'f58f98'],
	  [5, 'bd6758'],
	  [6, '660066'],
	  [7, '660000'],
	  [8, '6600FF'],
	  [9, '666633'],
	  [10, '990066'],
	  [11, '9900FF'],
	  [12, '996633'],
	  [13, '990000'],
	  [14, '9966FF'],
	  [15, '9999CC'],
	  [16, 'FF3333'],
	  [17, 'FF6699'],
	  [18, 'FF66FF'],
	  [19, 'FFFF99'],
	  [20, '680000'],
	  [21, '686868'],
	  [22, '7fb80e'],
	
	  [23, 'bed742'],
	  [24, 'b7ba6b'],
	  [25, '769149'],
	  [26, '6d8346'],
	  [27, 'cde6c7'],
	  [28, '1d953f'],
	  [29, '77ac98'],
	  [30, '007d65'],
	  [31, '45b97c'],
	  [32, '225a1f'],
	  [33, '2b6447'],
	  [34, '005831'],
	  [35, '006c54'],
	  [36, '274d3d'],
	  [37, '65c294'],
	  [38, '00ae9d'],
	  [39, '90d7ec'],
	  [40, '2468a2'],
	  [41, '1b315e'],
	  [42, 'decb00'],
	  [43, '596032'],
	  [44, '5f5d46'],
	  [45, 'ffd400'],
	  [46, 'b69968'],
	  [47, '6d5826'],
	  [48, 'ba8448'],
	  [49, 'e0861a'],
	  [50, 'c88400'],
	  [51, 'fab27b'],
	  [52, 'f47920'],
	  [53, 'fedcbd'],
	  [54, 'f36c21'],
	  [55, 'b7704f'],
	  [56, 'c99979'],
	  [57, '444693'],
	  [58, '2b4490'],
	  [59, '224b8f'],
	  [60, '46485f'],
	  [61, '6a6da9'],
	  [62, '494e8f'],
	  [63, '9b95c9'],
	  [64, '6950a1'],
	  [65, '594c6d'],
	  [66, '6f599c'],
	  [67, '401c44'],
	  [68, 'c77eb5'],
	  [69, 'f173ac'],
	  [70, '4f5555'],
	  [71, '563624'],
	  [72, '6c4c49'],
	  [73, '3c3645'],
	]);
	export default {
	  name: 'ProjectFile',
	  components: {
	    Title,
	  },
	  data() {
	    return {
	      description: '项目归档',
	      url: {
	        selectGit: '/article/blogArticle/selectGit',
	      },
	      projectFiles: [],
	    }
	  },
	  computed: {
	    getTagByArticle: function () {
	      return function (tag) {
	        return tag.split(",");
	      }
	    },
	  },
	  created() {
	    this.selectProjectFile();
	  },
	  methods: {
	     selectProjectFile(){
	       let param ={
	         path : '/Volumes/Mac/Respository/ProjectCode/.git',
	       }
	       postAction(this.url.selectGit,param).then(res=>{
	         if(res.data.success){
	           res.data.result.forEach((item, i) => {
	             this.projectFiles.push({
	               name: item.name,
	               commitMsg: item.commitMsg,
	               date: item.date,
	               type: this.randomColor(),
	               icon: this.randomIcon(),
	             })
	           });
	         }else{
	           this.$message({
	             message: '获取该月份文章失败 !',
	             type: 'warning'
	           });
	           console.log('获取该月份文章失败', e)
	         }
	       })
	     },
	    randomColor() {
	      return colorMap.get(Math.round(Math.random()*4));
	    },
	    randomIcon() {
	      return iconMap.get(Math.round(Math.random()*16));
	    },
	    randomFontColor() {
	      return "#"+fontColorMap.get(Math.round(Math.random()*73));
	    },
	  }
	}
	</script>
	<style scoped>
	  @import '../assets/Home.css';
	
	</style>
	```

4. 添加路由映射

	```javascript
	  {
	    path: '/blog/blogfile',
	    name: 'BlogFile',
	    component: BlogFile
	  },
	  {
	    path: '/blog/projectfile',
	    name: 'ProjectFile',
	    component: ProjectFile
	  }
	```

5. 后台对这两个页面中请求不做Shiro认证

	```java
	filterChainDefinitionMap.put("/article/blogArticle/groupByMonth", "anon");
	filterChainDefinitionMap.put("/article/blogArticle/seleteByMonth", "anon");
	filterChainDefinitionMap.put("/article/blogArticle/selectGit", "anon");
	```

6. fontColorMap和iconMap等js代码做模块化处理

	> 两个页面中都用到了fontColorMap等map，并且占用代码篇幅较大不美观。使用模块化处理统一管理。

	* 新建Map.js : 存放公用Map

		```javascript
		export default {
		  msg:'存放代码中用到的Map',
		  fontColorMap: new Map([//DarkVoilet
		    [0, 'f7acbc'],
		    [1, 'ef5b9c'],
		    [2, 'f05b72'],
		    [3, 'f8aba6'],
		    [4, 'f58f98'],
		    [5, 'bd6758'],
		    [6, '660066'],
		    [7, '660000'],
		    [8, '6600FF'],
		    [9, '666633'],
		    [10, '990066'],
		    [11, '9900FF'],
		    [12, '996633'],
		    [13, '990000'],
		    [14, '9966FF'],
		    [15, '9999CC'],
		    [16, 'FF3333'],
		    [17, 'FF6699'],
		    [18, 'FF66FF'],
		    [19, 'FFFF99'],
		    [20, '680000'],
		    [21, '686868'],
		    [22, '7fb80e'],
		
		    [23, 'bed742'],
		    [24, 'b7ba6b'],
		    [25, '769149'],
		    [26, '6d8346'],
		    [27, 'cde6c7'],
		    [28, '1d953f'],
		    [29, '77ac98'],
		    [30, '007d65'],
		    [31, '45b97c'],
		    [32, '225a1f'],
		    [33, '2b6447'],
		    [34, '005831'],
		    [35, '006c54'],
		    [36, '274d3d'],
		    [37, '65c294'],
		    [38, '00ae9d'],
		    [39, '90d7ec'],
		    [40, '2468a2'],
		    [41, '1b315e'],
		    [42, 'decb00'],
		    [43, '596032'],
		    [44, '5f5d46'],
		    [45, 'ffd400'],
		    [46, 'b69968'],
		    [47, '6d5826'],
		    [48, 'ba8448'],
		    [49, 'e0861a'],
		    [50, 'c88400'],
		    [51, 'fab27b'],
		    [52, 'f47920'],
		    [53, 'fedcbd'],
		    [54, 'f36c21'],
		    [55, 'b7704f'],
		    [56, 'c99979'],
		    [57, '444693'],
		    [58, '2b4490'],
		    [59, '224b8f'],
		    [60, '46485f'],
		    [61, '6a6da9'],
		    [62, '494e8f'],
		    [63, '9b95c9'],
		    [64, '6950a1'],
		    [65, '594c6d'],
		    [66, '6f599c'],
		    [67, '401c44'],
		    [68, 'c77eb5'],
		    [69, 'f173ac'],
		    [70, '4f5555'],
		    [71, '563624'],
		    [72, '6c4c49'],
		    [73, '3c3645'],
		  ]),
		  iconMap: new Map([
		    [0, 'el-icon-star-on'],
		    [1, 'el-icon-user-solid'],
		    [2, 'el-icon-platform-eleme'],
		    [3, 'el-icon-s-tools'],
		    [4, 'el-icon-s-goods'],
		    [5, 'el-icon-phone'],
		    [6, 'el-icon-success'],
		    [7, 'el-icon-circle-plus'],
		    [8, 'el-icon-remove'],
		    [9, 'el-icon-error'],
		    [10, 'el-icon-s-help'],
		    [11, 'el-icon-upload'],
		    [12, 'el-icon-picture'],
		    [13, 'el-icon-message-solid'],
		    [14, 'el-icon-s-platform'],
		    [15, 'el-icon-s-opportunity'],
		    [16, 'el-icon-dessert'],
		  ]),
		  colorMap: new Map([
		    [0, 'primary'],
		    [1, 'success'],
		    [2, 'info'],
		    [3, 'warning'],
		    [4, 'danger'],
		  ]),
		}
		```

	* 在BlogFile组件和ProjectFile组件中去掉map并引入Map.js

		```vue
		import Map from '../api/Map'
		之前使用的map都修改为Map.xxxMap
		```



## 为什么在vue项目中可直接使用es6语法糖

项目根目录有.babelrc文件，babel可将es6语法转为es5等浏览器完全支持的语法。并且在package.json中发现有

```json
"webpack": "^3.6.0",
"webpack-bundle-analyzer": "^2.9.0",
"webpack-dev-server": "^2.9.1",
"webpack-merge": "^4.1.0"
```

可将项目代码打包在浏览器使用。

