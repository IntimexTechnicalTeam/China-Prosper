<template>
  <div class="headerMain">
    <!-- 正常菜单 -->
      <div class="header-box header-normal">
        <div class="headerTop">
            <img class="slide-menu" src="/images/mobile/ptx_09.png" @click="showSlideMenu" />
            <ins-logo class="logoSide" />
            <div class="rightSide">
                 <router-link to="/account/GetEnquiry" v-if="isPtx" class="shoppingcart">
                    <i class="handle-icon ptxicon"></i>
                </router-link>
                <shopcart class="shoppingcart" v-show="!isPtx" />
                <ins-login />
            </div>
        </div>
      </div>
      <!-- 固定菜单 -->
      <div class="header-box header-fixed">
        <div class="headerTop">
            <img class="slide-menu" src="/images/mobile/ptx_09.png" @click="showSlideMenu" />
            <ins-logo class="logoSide" />
            <div class="rightSide">
                 <router-link to="/account/GetEnquiry" v-if="isPtx" class="shoppingcart">
                    <i class="handle-icon ptxicon"></i>
                </router-link>
                <shopcart class="shoppingcart" v-show="!isPtx" />
                <ins-login />
            </div>
        </div>
      </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component({
  components: {
    InsLogo: () => import('@/components/base/mobile/InsLogo.vue'),
    InsLogin: () => import('@/components/business/mobile/header/InsLogin.vue'),
    shopcart: () => import('@/components/business/mobile/header/InsShoppingCart.vue')
  }
})
export default class DefaultHeader extends Vue {
  searchKey: string = '';
  private key: string = '';
  private search: boolean = false;
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  showSlideMenu () {
    let isShow = !JSON.parse(JSON.stringify(this.menuShow));
    this.$store.dispatch('isShowMenu', isShow);
  }
  showsearch () {
    this.$store.dispatch('isShowSearch', true);
  }
  get showHomePage () {
    return this.$route.path;
  }
  get menuShow () {
    return this.$store.state.isShowMenu;
  }

  get isMobile () {
    return this.$store.state.isMobile;
  }
  get ShopCart () {
    return this.$store.state.shopCart;
  }
  created() {
    this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
     addEventListener('scroll', () => {
      var _this = this;
      let scrollTop = document.documentElement.scrollTop || window.pageYOffset || document.body.scrollTop;
      if (scrollTop >= 60) {
        $('.header-fixed').slideDown(300);
      } else {
        $('.header-fixed').fadeOut(300);
      }
    }, false);
  }
  @Watch('$route', { deep: true })
    onRouteChange () {
        this.$store.dispatch('isShowMenu', false);
    }
}
</script>

<style scoped lang="less">
  .header-fixed {
    width: 100%;
    position: fixed;
    left: 0;
    top: 0;
    z-index: 10000;
    background: #000;
    display: none;
  }
  .header-normal {
    z-index: 9999;
    width: 100%;
    position: absolute!important;
    left: 0px;
    top: 0px;
  }
  .searchMain {
    display: flex;
    float: left;
    align-items: center;
  }
  .searchImg {
        background: url('/images/mobile/mpic_08.png') no-repeat center center;
        display: inline-block;
        width: 2rem;
        height: 2rem;
        background-size: contain;
    }
    .header-box {
      background: url('/images/mobile/ptx_03.png') no-repeat center center;
      border-bottom-left-radius: 10px;
      border-bottom-right-radius: 10px;
      .flex-box {
        height: 7rem;
        background-color: #000;
        position: relative;
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 1.5rem;

        .logo {
          width: 15rem;
          margin: 0 auto;
        }

        .slide-menu {
          cursor: pointer;
          width: 2rem;
        }
        .close-meun {
          cursor: pointer;
          width: 2.5rem;
        }
      }
    }
.headerTop{
  width: 95%;
  padding-left: 2.5%;
  padding-right: 2.5%;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding-top: .5rem;
  padding-bottom: .5rem;
  display: flex;
  .logoSide {
    position: absolute;
    left: 50%;
    transform: translate(-50%);
  }
  .rightSide {
    float: right;
    display: flex;
  }
  .slide-menu {
    width: 2rem;
    height: 2rem;
    margin-top: .5rem;
  }
  .shoppingcart{
      padding-right: 0rem;
      float: left;
      height: 3.5rem;
      display: flex;
      align-items: center;
      justify-content: center;
    }
  .headerLang{
    width:20%;
    border-left: 1px solid #e6e6e6;
    position: absolute;
    right: 0px;
    height: 3.5rem;
    padding-left: 1rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .header-code{
    width:20%;
    border-left: 1px solid #e6e6e6;
    position: absolute;
    right: 25%;
    height: 3.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
.homePage {
    position: absolute!important;
    top: 0;
    z-index: 999;
    background: transparent!important;
    width: 100%;
  }
.ptxicon {
    width: 2.5rem;
    height: 2.5rem;
    background: url('/images/mobile/ptx_10.png') no-repeat center center;
    background-size: contain;
    display: inline-block;
    margin-left: .5rem;
  }
</style>
