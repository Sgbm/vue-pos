<template>
  <div id="app" style="position:absolute; left:0; top:0; width:100%;height:100%;">
    <!-- <el-header>header</el-header> -->
    <el-container style="height:calc(100%)" direction="horizontal">
      <el-aside width="100px">
        <leftNav/>
      </el-aside>
      <el-main style="padding:0">
        <Pos/>
      </el-main>
    </el-container>
    <!-- <el-footer>footer</el-footer> -->
  </div>
</template>

<script>
import leftNav from "@/components/common/LeftNav";
import Pos from "@/components/page/Pos";
export default {
  name: "app",
  components: {
    leftNav,
    Pos
  }
};
</script>

<style>
#app {
  text-align: center;
}
</style>
