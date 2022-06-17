<template>
  <div class="MemberPc">
    <!-- 正常购买模式 -->
    <div v-if="!isPtx">
      <accountHeader />
        <div class="MemberInfoNavPC">
          <ul class="NormalUl">
            <div @click="openlink('/account/memberInfo')">
            <li :class="NormalactiveClass == 1?'activeInfo':''" >
              <a>{{ $t("MemberInfo.MemberInfoTitle") }}</a>
            </li>
            </div>
            <div @click="openlink('/account/modifyPassword')">
            <li :class="NormalactiveClass == 2?'activeInfo':''" >
              <a>{{ $t("MemberInfo.ModifyPassword") }}</a>
            </li>
            </div>
            <div @click="openlink('/account/deliveryAddress')">
            <li :class="NormalactiveClass == 3?'activeInfo':''" >
              <a>{{ $t("Account.DeliveryAddress") }}</a>
            </li>
            </div>
          </ul>
        </div>
    </div>
    <!-- PTX询价模式 -->
    <div v-else>
      <div class="MemberInfoNavPC">
            <ul>
              <div @click="openlink('/account/memberInfo')">
              <li :class="activeClass == 1?'activeInfo':''" >
                <a>{{ $t("Enquiry.MyAccount") }}</a>
              </li>
              </div>
              <div @click="openlink('/account/modifyPassword')">
              <li :class="activeClass == 2?'activeInfo':''" >
                <a>{{ $t("MemberInfo.ModifyPassword") }}</a>
              </li>
              </div>
              <div @click="openlink('/account/message')">
              <li :class="activeClass == 3?'activeInfo':''" >
                <a>{{ $t("Account.LatestNews") }}</a>
              </li>
              </div>
              <div @click="openlink('/account/ptxorder')">
              <li :class="activeClass == 4?'activeInfo':''" >
                <a>{{ $t("Account.MyOrder") }}</a>
              </li>
              </div>
            </ul>
          </div>
    </div>
    <router-view></router-view>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component({
  components: {
    accountHeader: () =>
      import('@/components/hkTasteBusiness/pc/account/accountHeader.vue')
  }
})
export default class InsMenberCenter extends Vue {
    activeClass: any = 0;
    NormalactiveClass:any=0;
    openlink(link) {
    this.$router.push({ path: link });
  }
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  PtxisActive() {
    var url = this.$route.path;
    var reg = RegExp(/memberInfo/);
    if (reg.test(url)) {
      this.activeClass = 1;
    } else if (url.indexOf('modifyPassword') !== -1) {
      this.activeClass = 2;
    } else if (url.indexOf('message') !== -1) {
      this.activeClass = 3;
    } else if (url.indexOf('ptxorder') !== -1) {
      this.activeClass = 4;
    }
    console.log(this.activeClass);
  }
  NormalisActive() {
    var url = this.$route.path;
    var reg = RegExp(/memberInfo/);
    if (reg.test(url)) {
      this.NormalactiveClass = 1;
    } else if (url.indexOf('modifyPassword') !== -1) {
      this.NormalactiveClass = 2;
    } else if (url.indexOf('deliveryAddress') !== -1) {
      this.NormalactiveClass = 3;
    }
    console.log(this.NormalactiveClass);
  }
  updated() {
    this.PtxisActive();
    this.NormalisActive();
  }
  mounted() {
    this.PtxisActive();
    this.NormalisActive();
  }
}
</script>
<style lang="less">
#container .el-form-item__content {
  text-align: left !important;
}
.MemberPc {
  background: #f4f4f4;
  display: inline-block;
  background-size: 100% 100%;
  width: 100%;
}
.MemberPc .MemberInfoNavPC {
  width: 100%;
  display: inline-block;
  margin-top: 50px;
  margin-bottom: 20px;
  ul {
    width: 1000px;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    align-items: center;
    >div {
     width: 23%;
     li{
       padding-top: 10px;
       padding-bottom: 10px;
       background: #999999;
       border-radius: 20px;
       transition: all .3s;
       cursor: pointer;
       &:hover {
         background: #333;
       }
       a{
         display: flex;
         width: 100%;
         height: 100%;
         align-items: center;
         justify-content: center;
        color: #fff;
        font-size: 18px;
       }
     }
    }
  }
}
.MemberPc .activeInfo{
  color: #fff !important;
  background: #333333 !important;
}
.NormalUl {
  width: 800px!important;
  margin: 0 auto;
  >div{
    width: 32%!important;
  }
  li{
    width: 100%;
  }
}
</style>
