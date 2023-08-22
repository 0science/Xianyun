<template>
  <div>
    element-ui的演示
    <br>
    1.按钮
    <el-row>
        <el-button>默认按钮</el-button>
        <el-button type="primary">主要按钮</el-button>
        <el-button type="success" @click="open2">成功按钮</el-button>
        <el-button type="info">信息按钮</el-button>
        <el-button type="warning">警告按钮</el-button>
        <el-button type="danger" @click="open4">危险按钮</el-button>
    </el-row>

    <el-row>
        <el-button icon="el-icon-search" circle></el-button>
        <el-button type="primary" icon="el-icon-edit" circle></el-button>
        <el-button type="success" icon="el-icon-check" circle></el-button>
        <el-button type="info" icon="el-icon-message" circle></el-button>
        <el-button type="warning" icon="el-icon-star-off" circle></el-button>
        <el-button type="danger" icon="el-icon-delete" circle></el-button>
    </el-row>

  </div>
</template>

<script>
export default {
    methods:{
        open2(){
            this.$message(
                {
                    message:'恭喜你成功发送一条消息',
                    type:'success'
                }
            );
        },
        open4(){
            this.$message(
                {
                    message:'抱歉你发送消息失败',
                    type:'error'
                }
            );
        }
    }
}
</script>

<style>

</style>