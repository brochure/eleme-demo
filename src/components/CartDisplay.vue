<template>
  <el-row type="flex">
      <el-col :span="6">
        <img :src="genPicURL(meal.imgAddress)" class="logo">
      </el-col>
      <el-col :span="18" :offset="2">
        <el-row>
          <span><p style="font-size:0.86em;line-height:1.2em;">{{meal.title}}</p></span>
        </el-row>
          <el-row style="margin-top:-20px;">
                <!-- <li class="list-inline-item"><i class="mintui mintui-star" style="color:#ec6800;"></i><small style="color:#ec6800;">{{merchant.rate}}</small></li> -->
                <p class="rb-text-sm rb-text-muted">x{{meal.amount}}</p>
          </el-row>
          <el-row>
            <el-col :span="5">
                <p class="rb-text-sm rb-text-muted">¥{{meal.price}}</p>
            </el-col>
        </el-row>
      </el-col>
  </el-row>
</template>

<script>
import { PaletteButton } from 'mint-ui';

export default {
  props: {
    meal:{
      type: Object,
      default() {
        return {};
      }
    }
  },
  data () {
    return {
    }
  },
  methods: {
    genPicURL(pic) {
      return this.SERVER_BASE_URL + "/image/" + pic;
    }
  }
}
</script>

<style scoped>
 .logo{
    width: 70px;
    height: 70px;
    float: left;
  }

  .rb-text-sm{
    font-size: 0.85em;
    margin:0px;
    display: inline-block;
  }

  .rb-text-muted{
    color: #727171
  }
</style>
