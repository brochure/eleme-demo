<template>
  <div id="index">
  <el-container>
    <el-header>
      <header-bar header-title="个人资料"></header-bar>
    </el-header>
    <el-main>
      <p type="info">基础信息</p>
      <mt-cell title="头像" is-link @click.native="actionSheet">
        <img :src="genPicURL($store.getters.currentAccount.headPhotoAddress)" class="round_icon" />
        <!-- <img v-if="update" :src="profileImgUrl" class="round_icon" /> -->
      </mt-cell>
          <mt-cell title="用户名" is-link>
        <span style="color:#909399;">{{$store.getters.currentAccount.nickName}}</span>
      </mt-cell>
      <mt-cell title="收货地址" is-link />

      <p type="info" class="mt-4">账号绑定</p>
      <mt-cell title="手机" is-link to="/p_info/modifyPhoneNo">
        <span style="color:#909399;">{{replacePhoneNo($store.getters.currentAccount.phoneNo)}}</span>
      </mt-cell>
      <div v-for="item in thirdpartyBound" :key="item.providerName">
        <mt-cell :title="item.providerName" is-link>
          <span v-if="item.bound" class="bound">已绑定</span>
          <span v-else class="bound" style="color:#409EFF;">未绑定</span>
        </mt-cell>
      </div>
    </el-main>
    <mt-actionsheet
      :actions="actions"
      v-model="sheetVisible">
    </mt-actionsheet>
    <input ref="filElem" type="file" style="display:none" class="upload-file" @change="getFile">
  </el-container>
  </div>
</template>

<script>
  import HeaderBar from '../../components/HeaderBar.vue'
  import FileSaver from 'file-saver'

  var formData = new FormData()
  export default{
      name: 'index',
      components:{
        HeaderBar
      },
      data () {
        return {
          thirdpartyBound: [],
          update: true,
          profileImgUrl: "",
          // accountInfo:{},
          sheetVisible: false,
          actions: [{
          name: '拍照',
          method : this.getCamera
          }, {
            name: '从相册中选择', 
            method : this.getLibrary
          }],
          imgData: {
            accept: 'image/jpeg, image/png, image/jpg',
          },
        }
      },
    methods:{
      reloadProfileImg() {
        this.update = false;
        // this.getAccountInfo ();
      },
      genPicURL(pic) {        
        return this.SERVER_BASE_URL + "/image/" + pic;
      },
      updateAccount() {
        let that = this;
        let accountId = that.$store.getters.currentAccount.accountId;
        let url = that.HOST + "/account/id/" + accountId;
        let param = {
            accountId: accountId
        };
        that.$axios({
            url: url,
            method: 'POST',
            data: param
        }).then(resp => {
        if(resp.data.success){
            that.$store.commit('updateCurrentAccount',
                {
                    currentAccount: resp.data.content
                }
            );
        }else{
            that.$toast(resp.data.msg);
        }}).catch(err =>{
            that.$toast(err.data.msg);
        });
      },
      // getAccountInfo () {
      //   let that = this;
      //   let req_map = that.HOST + "/account/id/1";
      //   that.$axios({
      //     url: req_map,
      //     method: 'POST'
      //   }).then(resp => {            
      //     if(resp.data.success){
      //       that.accountInfo = resp.data.content;
      //     }else{that.$toast(resp.data.msg);}
      //   }).catch(err =>{
      //     console.log(err);
      //     reject(err.data);
      //   })
      // },
      replacePhoneNo (str) { // should be handed to the backstage
          if(!str){return ''}
          return str.replace(/(\d{3})\d{5}(\d{3})/, '$1****$2');
      },
      actionSheet(){this.sheetVisible = true;},
      getCamera(){
        console.log("打开照相机")
      },
      getLibrary(){
        this.$refs.filElem.dispatchEvent(new MouseEvent('click'))
      },
      getFile(){
        let that = this;
        function getObjectURL(file) { 
            var url = null; 
            if (window.createObjcectURL != undefined) { 
                url = window.createOjcectURL(file); 
            } else if (window.URL != undefined) { 
                url = window.URL.createObjectURL(file); 
            } else if (window.webkitURL != undefined) { 
                url = window.webkitURL.createObjectURL(file); 
            } 
            return url; 
        }
        let img = that.$refs.filElem.files[0];
        let type = img.type;
        let size = img.size;        
        if (that.imgData.accept.indexOf(type) == -1 || type == '') {
					that.$toast("图片格式错误，请重新上传。");
					return false;
				}
				if (size > 3145728) {
					that.$toast("请上传小于10M的图片");
					return false;
        }
        // let url = getObjectURL(this.$refs.filElem.files[0]);
        // TODO 应该用canvas编辑和保存图片
        let uri = ''
				let form = new FormData();
        form.append('file', img, img.name);
        let req_map = that.HOST + "/uploads/profile";
				that.$axios.post(req_map, form, {
					headers: {
						'Content-Type': 'multipart/form-data'
					}
				}).then(response => {
          // that.profileImgUrl = that.genPicURL(accountInfo.pic);
          that.updateAccount();
          // this.reloadProfileImg();
				}).catch(function(err) {
					console.log(err);
				});
      },
      getThirdpartyServiceProviderBound(){
        let that = this;
        let req_map = that.HOST + "/account/bound/1";
        that.$axios({
          url: req_map,
          method: 'POST'
        }).then(resp => {            
          if(resp.data.success){
            that.thirdpartyBound = resp.data.content;
          }else{that.$toast(resp.data.msg);}
        }).catch(err =>{
          console.log(err);
          reject(err.data);
        })
      }
    },
    created(){
      // this.getAccountInfo();
      this.getThirdpartyServiceProviderBound();
    },
    watch: {
      // accountInfo(val,oldVal) {
      //   this.$nextTick(() => {
      //     this.profileImgUrl = this.genPicURL(this.accountInfo.headPhotoAddress);
      //     this.update = true;
      //   })
      // }
    }
  }
</script>

<style scoped>
 .round_icon{
    width: 50px;
    height: 50px;
    display: flex;
    border-radius: 50%;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }
  .bound{
    font-size:0.8em;
  }
</style>