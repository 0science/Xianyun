<template>
  <div>
    vue基本知识复习
    <hr>
    1.插值表达式
    <br>
    {{ msg }}
    <br>
    {{ age +2 }}
    <br>
    {{ age >= 18? '成年' : '未成年'  }}
    <hr>
    2. v-test指令
    <br>
    <span v-text="msg"></span>
    <hr>
    3. v-html指令
    <br>
    <span v-html="msg"></span>
    <hr>
    4. v-on指令
    <button @click="ChangeMsg">点我修改</button>
    <hr>
    5. v-if/v-show
    <div v-if="flag">v-if</div>
    <div v-show="flag">v-show</div>
    <hr>
    6.v-bind
    <br>
    <img v-bind:src="imgsrc"  :title="'这是一张图 :${msg}'">
    <hr>
    7. v-model
    <div>
        用户名：<input type="text" v-model="name" v-bind:title="'${msg2}'"><br>
        密码：<input type="text" v-model="password" v-bind:title="'${msg3}'"><br>
        {{ name }}<br>
        <button @click="login">登录</button>
    </div>
    <hr>
    8.v-for
    <ul>
        <li v-for="(item,index) in persons" :key="item.id">{{index +'-->'+item.name + '--->'+ item.age}}</li> 
    </ul>
    <hr>
    9.axios
    <button @click="getImgs">axios获取图片数据</button>
   <ul>
        <li v-for="(item,index) in imgs" :key="index">
            <img :src="`http://210.21.98.31:6004${item.url}`"> 
        </li>
   </ul>



  </div>
</template>

<script>
import { Image } from 'element-ui';

export default {
    data() {
        return {
            msg: '<h3>hello world!</h3>',
            age: 17,
            flag:false,
            imgsrc:'https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png',
            name:'admin',
            password:'******',
            msg2:'请输入用户名',
            msg3:'请输入密码',
            persons:[{id:1,name:'jack',age:20},{id:2,name:'rose'}],
            imgs:[]
        };
    },
    methods: {
        ChangeMsg() {
            this.msg = 'hello 三亚学院';
        },
        login(){
            alert(this.name)
        },
        getImgs() {
          this.$axios({
            method:'get',
            url:'http://210.21.98.31:6004/scenics/banners'
          })
          .then(res =>{
            console.log(res.data.data);
            this.imgs = res.data.data;
          })
        }
    },
    components: { Image }
}
</script>

<style>
h3{
    color:red;
}
img{
    height: 50px;
}
</style>