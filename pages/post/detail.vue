<template>
    <el-row type="flex" justify="space-between" class="container">
      <div class="main">
        <!-- 面包屑标题 -->
        <div class="breadcrumb">
          <el-breadcrumb separator="/">
            <el-breadcrumb-item to="/post">旅游攻略</el-breadcrumb-item>
            <el-breadcrumb-item>攻略详情</el-breadcrumb-item>
          </el-breadcrumb>
        </div>
  
        <!-- 文章大标题 -->
        <h1>{{ infoData.title }}</h1>
  
        <!-- 文章信息 -->
        <div class="post-info">
          <span>攻略：{{ dateFormat(infoData.created_at) }} </span>
          <span>阅读：{{ infoData.watch }}</span>
        </div>
  
        <!-- 文章内容 -->
        <div class="post-content" v-html="infoData.content"></div>
  
        <!-- 文章分享 -->
        <div class="post-ctrl">
          <el-row type="flex" justify="center">
            <div class="ctrl-item">
              <i class="iconfont iconpinglun"></i>
              <p>评论({{ infoData.comments && infoData.comments.length }})</p>
            </div>
            <!-- <div class="ctrl-item" @click="handleStar">
                          <i class="iconfont iconstar1"></i>
                          <p>收藏</p>
                      </div> -->
            <div class="ctrl-item" @click="$message.warning('暂不支持分享')">
              <i class="iconfont iconfenxiang"></i>
              <p>分享</p>
            </div>
            <!-- <div class="ctrl-item" @click="handleLike">
                          <i class="iconfont iconding"></i>
                          <p>点赞({{detail.like || 0}})</p>
                      </div> -->
          </el-row>
        </div>
      </div>
  
      <div class="aside">
        <!-- 推荐文章 -->
        <h4 class="aside-title">相关攻略</h4>
        <div class="recommend-list">
          <nuxt-link v-for="item in toplist" :key="item.id"
          :to="`/post/detail?id=${item.id}`" class="recommend-item">
            <el-row type="flex" class="post-imgText">
              <el-row type="flex" align="middle" class="post-img">
                <img
                  :src="item.images&& item.images.length>0 && item.images[0]"
                  alt=""
                />
              </el-row>
              <div class="post-text">
                <div>{{ item.title }}</div>
                <p>{{ dateFormat(item.created_at) }} 阅读 {{ item.watch }}</p>
              </div>
            </el-row>
          </nuxt-link>
        </div>
      </div>
    </el-row>
  </template>
  
  <script>
  export default {
    data() {
      return {
        id: 0,
        infoData: {}, // 文章详情数据
        toplist:[]  //推荐文章列表 
      };
    },
    methods: {
      getTopList(){
          this.$axios.get('http://210.21.98.31:6004/posts/recommend').then(res=>{
              this.toplist = res.data.data;
          })
      },
      // 根据时间戳转成时间字符串显示，方便用户查看
      dateFormat(time) {
        let date = new Date(time);
        let year = date.getFullYear();
        let mouth = date.getMonth() + 1; // 因为getMonth()方法获取到的月份是少一个月的，所以要加回去
        let day = date.getDate();
        let hours = date.getHours();
        let mi = date.getMinutes();
     
      return `${year}-${mouth}-${day} ${hours}:${mi}`;
      },
      getInfoById() {
        this.$axios
          .get(`http://210.21.98.31:6004/posts?id=${this.id}`)
          .then((res) => {
            if (res.data.data && res.data.data.length > 0) {
              this.infoData = res.data.data[0];
            }
          });
      },
      init(){     
          // 打开loding
         const loading = this.$loading({       
            lock: true,
            text: '正在努力加载中，请耐心等待',
            spinner: 'el-icon-loading',
            background: 'rgba(255,255,255 0.3)'     
          });
  
          // 获取url传入的参数值
          // console.log(this.$route.query);
          this.id = this.$route.query.id;
          this.getInfoById();
          this.getTopList();
  
           // 关闭loding
          loading.close();
      }
    },
    watch:{
      // nuxt或者vue底层认为url路径没有改变，只是参数改变是不会引起视图更新的，所以需要使用watch来监听路由参数的改变从而重新触发数据获取
      $route() {
          this.init();
      }
    },
    mounted() {
      this.init();
    },
  };
  </script>
  
  <style scoped lang="less">
  .container {
    width: 1000px;
    margin: 0 auto;
    padding-top: 20px;
  }
  
  .main {
    width: 700px;
  
    h1 {
      padding: 20px 0;
      border-bottom: 1px #ddd solid;
    }
  
    .post-content {
      line-height: 1.5;
  
      /deep/ * {
        max-width: 700px !important;
      }
  
      /deep/ img {
        margin: 10px 0;
      }
    }
  
    .post-info {
      color: #999;
      padding: 20px;
      text-align: right;
  
      span {
        margin-left: 20px;
      }
    }
  
    .post-ctrl {
      padding: 50px 0 30px;
      .ctrl-item {
        margin: 0 20px;
        font-size: 20px;
        text-align: center;
        cursor: pointer;
  
        i {
          display: block;
          font-size: 28px;
          color: orange;
        }
  
        &:nth-child(2) {
          i {
            transform: scale(1.4);
          }
        }
        p {
          margin-top: 5px;
          font-size: 14px;
          color: #999;
        }
      }
    }
  
    /* 评论 */
    .cmt-wrapper {
      margin-bottom: 20px;
    }
  
    .replyTag {
      margin-bottom: 10px;
    }
  
    .cmt-title {
      font-weight: normal;
      font-size: 18px;
      margin-bottom: 20px;
    }
  
    .cmt-input {
      margin-bottom: 10px;
    }
  
    .cmt-input-ctrls {
      margin-bottom: 30px;
      /deep/ .el-upload--picture-card {
        width: 100px;
        height: 100px;
        line-height: 100px;
      }
  
      /deep/ .el-upload-list {
        li {
          width: 100px;
          height: 100px;
        }
  
        img {
          object-fit: cover;
        }
      }
    }
  
    .cmt-empty {
      color: #999;
      font-size: 14px;
      text-align: center;
      padding: 30px 0;
      border: 1px #ddd dashed;
    }
  
    .cmt-list {
      border: 1px #ddd solid;
    }
  
    .cmt-item {
      border-bottom: 1px #ddd dashed;
      padding: 20px 20px 5px;
  
      &:last-child {
        border-bottom: none;
      }
  
      .cmt-content {
        padding: 0 0 0 30px;
      }
  
      .cmt-info {
        margin-bottom: 10px;
        font-size: 12px;
        color: #666;
  
        * {
          vertical-align: top;
        }
  
        img {
          width: 16px;
          height: 16px;
          border-radius: 50%;
        }
  
        i {
          color: #999;
        }
        span {
          float: right;
        }
      }
      .cmt-message {
        margin-top: 10px;
      }
  
      .cmt-pic {
        width: 80px;
        height: 80px;
        border-radius: 6px;
        overflow: hidden;
        margin-right: 5px;
        margin-top: 10px;
        padding: 5px;
        border: 1px #ddd dashed;
  
        img {
          display: block;
          width: 100%;
          height: 100%;
          object-fit: cover;
          cursor: pointer;
        }
      }
  
      /*  这里和comment floor不一样，鼠标在最新内容上才hover */
      .cmt-new:hover {
        .cmt-ctrl {
          * {
            display: inline;
          }
        }
      }
  
      .cmt-ctrl {
        height: 20px;
        line-height: 20px;
        font-size: 12px;
        color: #1e50a2;
        text-align: right;
  
        * {
          display: none;
        }
  
        a:hover {
          text-decoration: underline;
        }
      }
    }
  }
  
  /* 侧边栏 */
  .aside {
    width: 280px;
  
    .aside-title {
      font-weight: normal;
      font-size: 18px;
      padding-bottom: 10px;
      border-bottom: 1px #ddd solid;
    }
  
    .recommend-item {
      display: block;
      padding: 20px 0;
      border-bottom: 1px #ddd solid;
  
      .post-imgText {
        height: 80px;
  
        .post-img {
          width: 100px;
          height: 80px;
          flex-shrink: 0;
          background: #ddd;
          overflow: hidden;
          margin-right: 10px;
          img {
            width: 100%;
            height: 100%;
            object-fit: cover;
          }
        }
  
        .post-text {
          flex: 1;
          position: relative;
          div {
            position: relative;
            line-height: 1.4em;
            /*设置容器高度为3倍行高就是显示3行*/
            height: 2.8em;
            overflow: hidden;
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
          }
  
          p {
            position: absolute;
            bottom: 0;
            left: 0;
            font-size: 12px;
            color: #999;
          }
        }
      }
    }
  }
  </style>
  