<template>
  <div class="container">
      <!-- 幻灯片 -->
      <el-carousel 
      :interval="5000" 
      arrow="always">
          <el-carousel-item 
          v-for="(item, index) in banners" 
          :key="index">
              <div class="banner-image" 
              :style="`
              background:url(${'http://210.21.98.31:6004'+item.url}) center center no-repeat;
              background-size:contain contain;
              `">
              </div>
          </el-carousel-item>
      </el-carousel>

      <!-- 搜索框 -->
    <div class="banner-content">
      <div class="search-bar">
        <el-row type="flex" class="search-tab">
          <span @click="tabClick('攻略')" ref="攻略" class="active">
            <i>攻略</i>
          </span>
           <span @click="tabClick('酒店')" ref="酒店">
            <i>酒店</i>
          </span>
           <span @click="tabClick('机票')" ref="机票">
            <i>机票</i>
          </span>
        </el-row>
        <el-row type="flex" align="middle" class="search-input">
          <input 
          placeholder="搜索城市"/>
          <i class="el-icon-search"></i>
        </el-row>
      </div>
    </div>


  </div>
</template>

<script>
export default {
  data(){
      return {
          // 轮播图数据
          banners: [
          {"url":"http://210.21.98.31:6004/assets/images/th01.jfif"},
          {"url":"http://210.21.98.31:6004/assets/images/th02.jfif"},
          {"url":"http://210.21.98.31:6004/assets/images/th03.jfif"},
          {"url":"http://210.21.98.31:6004/assets/images/th04.jfif"}        
          ]
      }
  },
  methods:{
    tabClick(typename){
        this.$refs['攻略'].classList.remove('active')
        this.$refs['酒店'].classList.remove('active')
        this.$refs['机票'].classList.remove('active')

        this.$refs[typename].classList.add('active')

        if(typename == '机票') {
                this.$router.push('/air')
            }
    },
    getBanners(){
        this.$axios.get("http://210.21.98.31:6004/scenics/banners").then(res=>{
            this.banners = res.data.data;
        })
    }
  },
  mounted(){
    this.getBanners();
  }
}
</script>

<style scoped lang="less">

.container{
  min-width:1000px;
  margin:0 auto;
  position:relative;

  .banner-content{
    z-index:9;
    width:1000px;
    position:absolute;
    left:50%;
    top:45%;
    margin-left: -500px;
    border-top:1px transparent solid;

    .search-bar{
      width:552px;
      margin:0 auto;
    }

    .search-tab{
      .active{
        i{
          color:#333;
        }
        &::after{
          background: #eee;
        }
      }

      span{
        width:82px;
        height:36px;
        display:block;
        position: relative;
        margin-right:8px;
        cursor: pointer;

        i{
          position:absolute;
          z-index:2;
          display: block;
          width:100%;
          height:100%;
          line-height:30px;
          text-align:center;
          color:#fff;
        }

        &:after{
          position: absolute;
          left:0;
          top:0;
          display:block;
          content: "";
          width:100%;
          height:100%;
          border: 1px rgba(255,255,255,.2) solid;
          border-bottom: none;
          transform: scale(1.1,1.3) perspective(.7em) rotateX(2.2deg);
          transform-origin: bottom left;
          background: rgba(0,0,0,.5);
          border-radius:1px 2px 0 0;
          box-sizing:border-box;
        }
      }
    }

    .search-input{
      width:550px;
      height:46px;
      background:#fff;
      border-radius: 0 4px 4px 4px;
      border: 1px rgba(255,255,255,.2) solid;
      border-top:none;
      box-sizing: unset;

      input{
        flex:1;
        height:20px;
        padding: 13px 15px;
        outline: none;
        border:0;
        font-size:16px;
      }

      .el-icon-search{
        cursor :pointer;
        font-size:22px;
        padding:0 10px;
        font-weight:bold;
      }
    }
  }  
}

/deep/ .el-carousel__container{
  height:600px;
}

.banner-image{
  width:100%;
  height:100%;
}
</style>