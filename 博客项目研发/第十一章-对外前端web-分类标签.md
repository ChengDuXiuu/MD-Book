## 分类标签实现

1. 新建Classify和Label组件

	```vue
	<template>
	  <div>
	    <el-container>
	      <el-header style="background: #333333;">
	        <el-row :gutter="24" style="height: 50px;">
	          <el-col :span="4"><div class="grid-content bg-purple" ></div></el-col>
	          <el-col :span="14"><div class="grid-content bg-purple" >
	            <Title ref="blogTitle"></Title>
	          </div></el-col>
	          <el-col :span="6"><div class="grid-content bg-purple" ></div></el-col>
	        </el-row>
	      </el-header>
	      <el-container>
	        <el-aside width="150px">
	          <el-row style="line-height: 50px;position:fixed; top:60px; ">
	            <div v-for="(item,index) in classifys">
	              <el-button plain round :type="randomColor()" :key="index" @click="selectByClassify(item.id)" v-if='index==0' style="margin: 0px 0px 0px 10px">
	                {{ item.name }}
	              </el-button>
	              <el-button plain round :type="randomColor()" :key="index" @click="selectByClassify(item.id)" v-else>
	                {{ item.name }}
	              </el-button>
	            </div>
	          </el-row>
	        </el-aside>
	        <el-main>
	          <a-empty v-if="flag"/>
	          <el-timeline>
	            <el-timeline-item :color="randomColor()"  placement="top" :timestamp="article.time" size="large" v-for="article in articles">
	              <el-card>
	                <router-link target="_blank" :to="{name:'BlogDetail',params:{blogId:article.id}}">
	                  <a onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'" style="font-size: 150%;">
	                    {{ article.title}}
	                  </a>
	                </router-link>
	                <br/>
	                <br/>
	                <el-tag :type="randomColor()" v-for="tag in getTagByArticle(article.tag)" style="margin-right: 5px">
	                  {{ tag }}
	                </el-tag>
	              </el-card>
	            </el-timeline-item>
	          </el-timeline>
	        </el-main>
	      </el-container>
	    </el-container>
	  </div>
	</template>
	
	<script>
	  import Title from "../components/Title";
	  import Map from '../api/Map'
	  import {getAction,postAction} from "../api/manage";
	
	  export default {
	      name: "Classify",
	      components: {
	        Title,
	      },
	      data() {
	        return {
	          description: '分栏',
	          flag:false,
	          url: {
	            listAll: '/special/blogSpecialColumn/listAll',
	            columnArticleData: '/special/blogSpecialColumn/columnArticleData',
	          },
	          classifys: [],
	          articles: [],
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
	        this.getAllClassify();
	      },
	      methods: {
	        //获取所有分栏
	        getAllClassify(){
	          this.articles=[];
	          getAction(this.url.listAll)
	            .then((res) => {
	              console.log("获取所有分栏 》》",res)
	              if (res.data.success) {
	                res.data.result.forEach((item, i) => {
	                  this.classifys.push({
	                    id:item.id,
	                    name: item.specialName,
	                  })
	                })
	                this.selectByClassify(this.classifys[0].id);
	              }
	            })
	            .catch((e) => {
	              this.$message({
	                message: '获取所有分栏失败 !',
	                type: 'warning'
	              });
	              console.log('获取所有分栏失败', e)
	            })
	        },
	        selectByClassify(id){
	          this.articles=[];
	          getAction(this.url.columnArticleData,{column:id})
	            .then((res) => {
	              console.log("获取分栏下数据 》》",res)
	              if (res.data.success) {
	                res.data.result.forEach((item, i) => {
	                  this.flag=false;
	                  this.articles.push({
	                    id: item.id,
	                    title: item.title,
	                    tag: item.tag,
	                    time:item.createTime,
	                  })
	                })
	              }else{
	                this.flag=true;
	                // this.$message({
	                //   message: res.data.message,
	                //   type: 'warning',
	                //   duration:'1000'
	                // });
	              }
	            })
	            .catch((e) => {
	              this.$message({
	                message: '获取分栏下数据失败 !',
	                type: 'warning',
	                duration:'1000'
	              });
	              console.log('获取分栏下数据失败', e)
	            })
	        },
	        randomColor() {
	          return Map.colorMap.get(Math.round(Math.random()*4));
	        },
	      }
	    }
	</script>
	
	<style scoped>
	  @import '../assets/Home.css';
	</style>
	
	```

	```vue
	<template>
	  <div>
	    <el-container>
	      <el-header style="background: #333333;">
	        <el-row :gutter="24" style="height: 50px;">
	          <el-col :span="4"><div class="grid-content bg-purple" ></div></el-col>
	          <el-col :span="14"><div class="grid-content bg-purple" >
	            <Title ref="blogTitle"></Title>
	          </div></el-col>
	          <el-col :span="6"><div class="grid-content bg-purple" ></div></el-col>
	        </el-row>
	      </el-header>
	      <el-container>
	        <el-aside width="150px">
	          <el-row style="line-height: 50px;position:fixed; top:60px; ">
	            <div v-for="(item,index) in labels">
	              <el-button plain round :type="randomColor()" :key="index" @click="selectByClassify(item.id)" v-if='index==0' style="margin: 0px 0px 0px 10px">
	                {{ item.name }}
	              </el-button>
	              <el-button plain round :type="randomColor()" :key="index" @click="selectByClassify(item.id)" v-else>
	                {{ item.name }}
	              </el-button>
	            </div>
	          </el-row>
	        </el-aside>
	        <el-main>
	          <a-empty v-if="flag"/>
	          <el-timeline>
	            <el-timeline-item :color="randomColor()"  placement="top" :timestamp="article.time" size="large" v-for="article in articles">
	              <el-card>
	                <router-link target="_blank" :to="{name:'BlogDetail',params:{blogId:article.id}}">
	                  <a onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'" style="font-size: 150%;">
	                    {{ article.title}}
	                  </a>
	                </router-link>
	                <br/>
	                <br/>
	                <el-tag :type="randomColor()" v-for="tag in getTagByArticle(article.tag)" style="margin-right: 5px">
	                  {{ tag }}
	                </el-tag>
	              </el-card>
	            </el-timeline-item>
	          </el-timeline>
	        </el-main>
	      </el-container>
	    </el-container>
	  </div>
	</template>
	
	<script>
	  import Title from "../components/Title";
	  import Map from '../api/Map'
	  import {getAction,postAction} from "../api/manage";
	  export default {
	    name: "Label",
	    components: {
	      Title,
	    },
	    data() {
	      return {
	        description: '分栏',
	        flag: false,
	        url: {
	          listAll: '/labels/blogLabels/listAll',
	          columnArticleData: '/labels/blogLabels/labelArticleData',
	        },
	        labels: [],
	        articles: [],
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
	      this.getAllClassify();
	    },
	    methods: {
	      //获取所有分栏
	      getAllClassify(){
	        this.articles=[];
	        getAction(this.url.listAll)
	          .then((res) => {
	            console.log("获取所有标签 》》",res)
	            if (res.data.success) {
	              res.data.result.forEach((item, i) => {
	                this.labels.push({
	                  id:item.id,
	                  name: item.labelName,
	                })
	              })
	              this.selectByClassify(this.labels[0].id);
	            }
	          })
	          .catch((e) => {
	            this.$message({
	              message: '获取所有标签失败 !',
	              type: 'warning',
	              duration:'1000'
	            });
	            console.log('获取所有标签失败', e)
	          })
	      },
	      selectByClassify(id){
	        this.articles=[];
	        getAction(this.url.columnArticleData,{column:id})
	          .then((res) => {
	            console.log("获取标签下数据 》》",res)
	            if (res.data.success) {
	              res.data.result.forEach((item, i) => {
	                this.flag=false;
	                this.articles.push({
	                  id: item.id,
	                  title: item.title,
	                  tag: item.tag,
	                  time:item.createTime,
	                })
	              })
	            }else{
	              this.flag=true;
	              // this.$message({
	              //   message: res.data.message,
	              //   type: 'warning',
	              //   duration:'1000'
	              // });
	            }
	          })
	          .catch((e) => {
	            this.$message({
	              message: '获取标签下数据失败 !',
	              type: 'warning',
	              duration:'1000'
	            });
	            console.log('获取标签下数据失败', e)
	          })
	      },
	      randomColor() {
	        return Map.colorMap.get(Math.round(Math.random()*4));
	      },
	    }
	  }
	</script>
	
	<style scoped>
	  @import '../assets/Home.css';
	</style>
	
	```

2. 新增路由映射

	```js
	import Classify from '../views/Classify'
	import Label from '../views/Label'
	{
	  path: '/blog/classify',
	  name: 'Classify',
	  component: Classify
	},
	{
	  path: '/blog/label',
	  name: 'Label',
	  component: Label
	},
	```



3. Title组件中修改路由跳转

	```vue
	<router-link  :to="{name:'Classify'}">
	  <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">分类</span>
	</router-link>
	<router-link  :to="{name:'Label'}">
	  <span style="margin-right: 20px;margin-left: 20px;color: #f9fafc" onmouseover="this.style.color='#b2d235'" onmouseout="this.style.color='#426ab3'">标签</span>
	</router-link>
	```

4. ArticleLabelRepository类中新增

	```java
	List<ArticleLabels> findArticleLabelsByLabelId(String labelId);
	```

5. BlogLabelsConfig类中新增

	```java
	 /*
		  * <p>获取标签下所有文章 </p>
		  * @author mac
		  * @date 2020/12/1 10:21 下午
		  * @param column
		  * @return org.jeecg.common.api.vo.Result<?>
		  */
		 @AutoLog(value = "blog_special_column-获取标签下所有文章")
		 @ApiOperation(value="blog_special_column-获取标签下所有文章", notes="blog_special_column-获取标签下所有文章")
		 @GetMapping(value = "/labelArticleData")
		 public Result<?> columnArticleData(@RequestParam(name="column",required=true) String columnId){
			 List<ArticleLabels> articleLabelsList = articleLabelRepository.findArticleLabelsByLabelId(columnId);
			 List<String> articleIdList = articleLabelsList.stream().map(e -> e.getArticleId()).collect(Collectors.toList());
			 if (articleIdList.isEmpty()) return Result.error("该标签下没有博客");
			 Collection<BlogArticle> blogArticleList = blogArticleService.listByIds(articleIdList);
	
			 return Result.ok(blogArticleList);
		 }
	```

	

