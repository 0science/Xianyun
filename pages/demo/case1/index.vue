<template>
  <div>
    <hr>
    <div class="btn">
        <button @click="toggle">点击隐藏/显示</button>
    </div>
    <div class="shiju" v-show="flag">
        <ul>
            <li>{{ msg1 }}</li>
            <li>{{ msg2 }}</li>
            <li>{{ msg3 }}</li>
            <li>{{ msg4 }}</li>
        </ul>
    </div>
    <hr>
  </div>
</template>

<script>
export default {
    data(){
        return {
            flag:true,
            msg1:'床前明月光',
            msg2:'疑是地上霜',
            msg3:'举头望明月',
            msg4:'低头思故乡'
        }
    },
    methods:{
        toggle(){
            this.flag = !this.flag;
        }
    }
}
</script>

<style>

</style>