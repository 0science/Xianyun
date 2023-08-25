<template>
  <div class="container">
    <el-row type="flex" justify="space-between">
     
      <div class="menus-wrapper">
        <div class="menus-body" @mouseleave="leaveHander">
        <!-- 左侧一级菜单栏 -->
          <div class="menus">
            <div v-for="(item,index) in cities" :key="index" class="menu-item"
            @mouseenter="enterHander(index)"
            :class="{active:index==currentIndex}">
              <span>{{ item.type }}</span>
            </div>
          </div>
          <!-- 左侧二级菜单 使用mouseenter 和 mouseleave 来控制显隐 -->
          <div class="sub-menus" v-show="isshow">
            <ul>
              <li v-for="(item,index) in citiesChildren" :key="index">
                <nuxt-link :to="`/post?city=${item.city}`">
                  <i>{{ index + 1 }}</i>
                  <strong>{{item.city }}</strong>
                  <span>{{ item.desc }}</span>
                </nuxt-link>
              </li>              
            </ul>
          </div>
        </div>
        
        <!-- 左边下面推荐城市 -->
        <div class="aside-recommend">
          <h4 class="aside-title">推荐城市</h4>
          <nuxt-link to="#" class="aside-recommend-item">
            <img src="http://fe-xianyun-web.itheima.net/images/pic_sea.jpeg"/>
          </nuxt-link>
        </div>
      </div>

      <!-- 右侧内容 -->
      <div class="post-wrapper">
        <!-- 搜索和推荐区域 -->
        <div  class="search-wrapper">
          <el-row type="flex" justify="space-between" align="middle"  class="search-bar"> 
            <input 
            type="text" 
            @click.enter="setSearch(null)"
            v-model="searchCity"
            placeholder="请输入想去的地方，比如：'广州'">
            <i class="el-icon-search" @click="setSearch(null)"></i>
          </el-row>
          <div class="search-recommend">
            推荐：
            <span 
                v-for="(item, index) in [`广州`, `上海`, `北京`]" 
                :key="index"
                @click="setSearch(item)">                
              {{item }}
            </span>
          </div>
        </div>       
          
         <!-- 推荐攻略区域 -->
        <el-row type="flex" justify="space-between"  align="middle" class="post-title">
          <h4  >推荐攻略</h4>
          <el-button 
          type="primary"
          icon="el-icon-edit">
          写游记
          </el-button>
        </el-row>
        <el-row>
           <el-pagination
            @size-change="handerSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="currentPage"          
            :page-size="limit"
            layout="prev, sizes,pager, next"
            :total="total">
          </el-pagination>
        </el-row>

        <!-- 游记列表 -->
        <div class="post-list" >
          <!-- 带有图片列表 -->
          <div v-for="(item,index) in list" :key="item.id" class="post-item card">
            <!-- 游记标题 -->
              <h4 title="data.title" class="post-title">
                  <nuxt-link :to="`/post/detail?id=${item.id}`">
                    {{ item.title }}
                  </nuxt-link>
              </h4>
              <!-- 游记描述 -->
              <p class="post-desc">
                  <nuxt-link :to="`/post/detail?id=${item.id}`">
                  {{ item.summary }}
                  </nuxt-link>
              </p>

              <!-- 图片列表 -->
              <el-row type="flex" 
                      justify="space-between" 
                      align="middle"  
                      class="card-images">
            
                  <img v-for="(imgsrc,index) in item.images" :key="index" 
                  :src="imgsrc">
                  <!-- <img src="https://images.mafengwo.net/images/i/face/brands_v3/6@2x.png">
                  <img src="https://p1-q.mafengwo.net/s10/M00/E9/33/wKgBZ1octwiAAKAoAAJ9ixcJc9M71.jpeg?imageView2%2F2%2Fw%2F1360%2Fq%2F90"> -->
            
              </el-row >

              <el-row type="flex" justify="space-between" class="post-info">
                  <el-row type="flex" align="middle" class="post-info-left">
                      <span>
                          <i class="el-icon-location-outline"></i> 
                          {{ item.city.name }}
                      </span>
                      <el-row type="flex" align="middle" class="post-user">
                          by 
                          <nuxt-link to="/user/personal">
                              <img src="http://210.21.98.31:6004/assets/images/avatar.jpg">
                          </nuxt-link>
                          <nuxt-link to="/user/personal">
                            {{ item.account.nickname }}
                          </nuxt-link>
                      </el-row>
                      <span>
                          <i class="el-icon-view">{{ item.watch}}</i> 查看次数
                      </span>
                  </el-row>
                  <span class="post-info-right">
                      {{ item.like }} 赞
                  </span>
              </el-row>
          </div>
        </div>

        <el-row>
           <el-pagination
            @current-change="handleCurrentChange"
            :current-page.sync="currentPage"
            :page-size="limit"
            layout="prev, sizes,pager, next"
            :total="total">
          </el-pagination>
        </el-row>
      </div>
    </el-row>
  </div>
</template>

<script>

export default {
  head: {
    title: '闲云旅游攻略路书下载,旅游攻略,自助游,自由行攻略指南 - 闲云旅游网',
    meta: [
      {
        hid: 'description',
        name: 'description',
        content: '闲云旅游免费提供世界各地精彩的旅游攻略路书下载,攻略下载后可一键打印随身携带.旅游攻略信息由真实的旅行用户志愿提供,闲云旅游攻略,自助游,自由行攻略指南.'
      },
      {
        hid: 'keywords',
        name: 'keywords',
        content: '旅游攻略下载,自助游攻略,自由行攻略,路书,旅游指南'
      }
    ],
  },
  data(){
    return {
      isshow:false,
      start:0,
      limit:3,
      total:0,
      currentPage:1, // 分页组件当前是第几页，默认是第一页
      currentIndex:3,
      cities:[], // 左边一级菜单和二级菜单的数据保存地方
      citiesChildren:[], //二级菜单的数据
      list:[],  // 游记数据
      searchCity:'北京',  // 搜索的条件数据，城市参数
    }
  },
  methods:{
    handerSizeChange(pageCount){
      // 当用户切换每页显示的条数时触发
      // 需求：
      /** 
       * 1. 修改data函数中的limit属性值为当前用户选择的没页显示的条数pageCount
       * 2. 重置当前页面为第1页
       * 3.调用getList()重新发送新的参数获取新的数据
       * */ 
      this.limit = pageCount;
      this.currentIndex = 1;
      this.start = 0;
      this.getList();
    },
    handleCurrentChange(curPage) {
      // 当用户切换分页页码的时候会触发
      // console.log('当前页=',curPage);
      // 逻辑分解
      /*
        1. 根据curPage(当前页码数字) 通过 (当前页码- 1) *每页条数 获得需要跳过的数据条数
        2. 调用getList()重新发出数据请求，获取当页数据
        3. 修改getList()函数的分页逻辑，增加_start 和 _limit参数才能实现真正的数据分页
      **/ 
     this.start = (curPage - 1) * this.limit;
     this.getList();

    },
    setSearch(city){
      // 作用：用来接受用户输入的搜索城市条件数据,重新搜索数据
      // 注意点:在调用getList之前，请将searchCity使用v-model与搜索input绑定

      if(city) {
        // 有值才设置
        this.searchCity = city
      }

      this.getList();

    },
    // 获取一级菜单和二级菜单的数据
    getCities() {
     
      this.$axios.get('http://210.21.98.31:6004/posts/cities')
      .then(res=>{
        this.cities = res.data.data;
      })
    },
    // 获取游记功能数据
    getList() {     
      //  get 请求 http://210.21.98.31:6004/posts
      // 打开loding
       const loading = this.$loading({
          target:'.search-wrapper',
          lock: true,
          text: '正在努力加载中，请耐心等待',
          spinner: 'el-icon-loading',
          background: 'rgba(255,255,255 0.3)'     
        });

    // 3. 修改getList()函数的分页逻辑，增加_start 和 _limit参数才能实现真正的数据分页
  let url =`http://210.21.98.31:6004/posts?city=${this.searchCity}&_limit=${this.limit}&_start=${this.start }`


      this.$axios.get(url)
      .then(res=>{
         this.list = res.data.data;     
        //动态化分页条的总条数，来源于服务器返回的数据
         this.total = res.data.total;
        // 关闭loding
        loading.close();
      })
    },
    enterHander(index) {
      // 控制二级菜单显示出来
      this.isshow = true;
      this.currentIndex = index;
      //  根据索引获取到二级菜单数据
      this.citiesChildren = this.cities[index].children;
    },
    leaveHander(){
      // 控制鼠标离开一级菜单后二级菜单要隐藏
      this.isshow = false;
    }
  },
  mounted(){
    // 当旅游攻略页面挂载完成后，触发数据获取方法
    this.getCities();
    this.getList(); 
  }
}
</script>

<style>
 .el-loading-spinner .el-loading-text{
  color:orange;
  font-size: 16px;
 }
</style>

<style scoped lang="less">

.post-item{
    width:100%;
    padding:20px 0;
    border-bottom:1px #eee solid;
}

.image-text{
    .post-content{
        flex: 1;
    }
    .post-desc{
    }
}


.post-cover{
    width:220px;
    height:150px;
    overflow: hidden;
    flex-shrink: 0;
    margin-right:10px;

    img{
        display:block;
        width:100%;
        height: 100%;
        object-fit: cover;
    }
}

.post-title{
    overflow: hidden;
    text-overflow:ellipsis;
    white-space: nowrap;
    margin-bottom:15px;
    font-weight: normal;
    font-size:18px;

    a:hover{
        color:orange;
    }
}

.post-desc{    
    margin-bottom: 15px;
    line-height: 1.5;
    font-size:14px;
    height: 1.5 *14px * 3;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient:vertical;

    a{
        color:#666;
    }
}




.post-user{
     img{
         display:block;
         width:16px;
         height:16px;
         border-radius: 100px;
         margin: 5px;
     }

     a{
         color:orange;
     }
}

.post-info-left{
     font-size: 12px;
     color:#999;

     > *{
         margin-right:10px;
     }
 }

 .post-info-right{
     color:orange;
 }

 .card-images{
     margin:15px 0;
     img{
        width:220px;
        height:150px;
        display: block;
        object-fit: cover;
     }
 }
</style>
<style  lang="less">
  .container{
    width:1000px;
    margin:0 auto;
    padding:20px 0;
  }

  /* 菜单栏 */
  .menus-wrapper{
    position: relative;
    width:260px;
    z-index:2;

    .menus{
      width:260px;
      border:1px #ddd solid;
      border-right:none;
      border-bottom:none;
      box-shadow: 0 0 1px #f5f5f5;
      z-index:2;
    }

    .menu-item{
      height:40px;
      line-height: 40px;
      border-bottom:1px #ddd solid;
      border-right:1px #ddd solid;
      padding:0 20px;
      font-size:14px;
      position: relative;
      z-index: 2;

      &:after{
        display: block;
        content:"";
        width:10px;
        height:10px;
        border-right:1px #999 solid;
        border-top:1px #999 solid;
        transform: rotate(45deg);
        position: absolute;
        right:20px;
        top:15px;
      }

      &.active{
        border-right-color: #fff;
        color:orange;

        &:after{
          border-right-color: orange;
          border-top-color: orange;
        }
      }
    }

    .sub-menus{
      position: absolute;
      left:260px;
      top:0;
      width:350px;
      padding:10px 20px;
      box-sizing: border-box;
      background: #fff;
      border: 1px #ddd solid;
      ul{
        margin: 0px;
        padding: 0px;
      }

      ul li{
        font-size: 14px;
        line-height: 1.5;
        list-style: none;

        *{
          vertical-align: middle;
        }

        i {
          color:orange;
          font-size: 24px;
          font-style: italic;
        }

        strong{
          margin:0 10px;
          color: orange;
          font-weight: normal;
          &:hover{
            text-decoration: underline;
          }
        }

        span{
          color:#999;
          &:hover{
            text-decoration: underline;
          }
        }
      }
    }
  }

  /* 侧边栏推荐 */
  .aside-recommend{
    margin-top: 20px;
    
    .aside-title{
      font-weight: normal;
      padding-bottom: 10px;
      border-bottom: 1px #ddd solid;
      margin-bottom:10px;
    }

    .aside-recommend-item{
      img{
        width:100%;
        display: block;
      }
    }
  }

  /*内容*/
  .post-wrapper{
    width:700px;
  }

  /*搜索栏*/
  .search-wrapper{
    .search-bar{
      width:100%;
      box-sizing: border-box;
      height:40px;
      line-height: 40px;
      border:3px orange solid;

      input{
        flex:1;
        padding:0 20px;
        line-height: 40px;
        outline: none;
        border:none;
        background:none;
      }

      i{
        font-size: 24px;
        color:orange;
        font-weight: bold;
        margin-right:10px;
      }
    }

    .search-recommend{
      padding:10px 0;
      font-size: 12px;
      color:#666;

      span{
        margin-right:5px;
        &:hover{
          color:orange;
          text-decoration: underline;
          cursor: pointer;
        }
      }
    }
  }

  /* 标题 */
  .post-title{
    padding-bottom:10px;
    border-bottom:1px #eee solid;
    position: relative;

    h4{
      font-weight: normal;
      font-size: 18px;
      color:orange;

      &:after{
        display:block;
        content: "";
        width:72px;
        height:2px;
        background:orange;
        position: absolute;
        bottom:0;
        left:0;
      }
    }
  }
</style>