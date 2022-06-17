<template>
 <div id="footer">
    <div class="footer-box">
          <p class="footerLogo"><img src="/images/mobile/ptx_04.png"></p>
          <div class="footerNav">
            <div id="menu">
                <Menu  :textColor="'#fff'" :uniqueOpened="true" :type="'footer'" />
            </div>
              </div>
          <div class="footerAccept" v-if="!isPtx">
            <p>{{$t('home.Weaccept')}}</p>
            <div>
              <img src="/images/payment/stripe.png" />
              <img src="/images/payment/WeChatPay.png" />
              <img src="/images/payment/Alipay.png" />
              <img src="/images/payment/PayMe.png" />
              <img src="/images/payment/Paypal.png" />
              <img src="/images/payment/MasterCard.png" />
              <img src="/images/payment/VISA.png" />
            </div>
          </div>
          <div class="footerCpy">
            <p>Copyright © {{currentYear}} Prosper Bird's Nest & Ginseng Co.<br>powered by Eventizer
            <a href="https://eventizer.hk/" target="_blank">
              <img src="/images/mobile/footerlogo.png">
            </a>
            </p>
          </div>
    </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component({
  components: {
    Menu: () => import('@/components/business/mobile/header/InsElMenu.vue')
    }
})
export default class InsFooter extends Vue {
  currentYear: number = 0;
  clickIndex: number = 0;
  footerMenus: any[] = [];
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  showMeun (item, index) {
    $('.sub' + index).slideToggle();
  }
  closeSlideMenu (n) {
    this.$store.dispatch('isShowMenu', false);
  }
  To (n) {
    return n.Type === 1 ? '/cms/catDetail/' + n.Value.Id : n.Type === 2 ? '/CMS/content/' + n.Value.Id : n.Type === 3 ? '/RegNPay/Form/' + n.Value.Id : n.Type === 4 && !this.$store.state.catMenuType ? '/product/cat/' + n.Value.Id : n.Type === 4 && this.$store.state.catMenuType ? '/product/search/-?catalogs=' + JSON.stringify([parseInt(n.Value.Id)]) + '&type=0' : n.Type === 5 ? '/product/search/-?attrs=' + JSON.stringify([{ Id: parseInt(n.Value.Id), Vals: [] }]) + '&type=0' : '/product/search/-?attrs=' + JSON.stringify([{ Id: parseInt(n.ParentId), Vals: [parseInt(n.Value.Id)] }]) + '&type=0';
  }
  getMenu () {
    this.$Api.promotion.getMenu().then((result) => {
      this.footerMenus = result.ReturnValue.FooterMenus;
    });
  }
  created () {
    var date = new Date();
    this.currentYear = date.getFullYear();
    this.getMenu();
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    $('.submeunMain').hide();
  }
}
</script>
<style lang="less">
#footer {
  #menu {
      .el-submenu__icon-arrow {
          display: none;
      }

      .el-submenu__title {
          padding-top: 0.5rem;
          padding-bottom: 0.5rem;
          height: auto!important;
          line-height: unset;
          background-color:transparent!important;
          background-size: cover;
          .name{
              font-size: 1.6rem!important;
              color:#fff;
          }
      }
       > .el-menu {
          width: 100%;
          margin: 0 auto;
          background-color: transparent!important;
          border: 0;
          .el-submenu__icon-arrow {
              display: block;
              font-size: 1.6rem;
              color: #fff;
              right: 30px;
          }

          > li {
              height: auto;
              line-height: unset;
              text-align: center;
              background: transparent!important;
              border-bottom: 1px solid #b36b7c;
              >a {
                  color:#ffffff;
                  display:block;
                  width: 100%;
                  padding-top: 1rem;
                  padding-bottom: 1rem;
                  margin: 0 auto;
                  font-weight: 500;
                  background-color:transparent!important;
                  b{
                      color:#FFF;
                      display: block;
                      width: 100%;
                      font-weight: 500;
                      &:nth-child(1){
                          color:#ffffff;
                          font-weight: 500;
                          font-size: 1.6rem;
                      }
                      &:nth-child(2){
                          color:#ffffff;
                          font-size: 1.2rem;
                      }
                  }
              }

              a {
                  text-decoration: none;
              }
          }

          li {
              line-height: unset;
              padding: 0 !important;
              min-width: unset;
          }
      }
  }
  .el-menu--inline {
    width: 100%!important;
    background-color: #fff!important;
          > li {
              height: auto;
              line-height: unset;
              text-align: center;
              background: transparent!important;
              border-bottom: 1px solid #eee;
              >a {
                  color:#ffffff;
                  display:block;
                  width: 100%;
                  padding-top: 1rem;
                  padding-bottom: 1rem;
                  margin: 0 auto;
                  font-weight: 500;
                  background-color:transparent!important;
                  b{
                      color:#FFF;
                      display: block;
                      width: 100%;
                      font-weight: 500;
                      &:nth-child(1){
                          color:#333;
                          font-weight: 500;
                          font-size: 1.6rem;
                      }
                      &:nth-child(2){
                          color:#333;
                          font-size: 1.2rem;
                      }
                  }
              }
            .router-link-exact-active {
                background: url('/images/mobile/ptx_27.png') no-repeat center center!important;;
                background-size: cover!important;
                b{
                  color: #b19162!important;
                }
            }
              a {
                  text-decoration: none;
              }
          }

    // >li{
    //   border-bottom: 1px solid #eee;
    //   margin-bottom: 0px!important;
    //   > a{
    //     background: transparent!important;
    //   }
    // }
  }
  #menu .is-opened > .el-submenu__title{
      background-color: transparent!important;
      .name{
          color:#fff!important;
      }
  }
  #menu .is-opened > .el-submenu__title .el-submenu__icon-arrow{
      color:#fff!important;
  }
}
</style>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.submeunMain{
  display: none;
}
.SubMeun0{
  display: none;
}
.SubMeun1{
  display: none;
}
#footer{
  width: 100%;
  background: url('/images/mobile/ptx_02.png') no-repeat center center;
  background-size: cover;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
  .footer-box{
    width: 90%;
    margin: 0 auto;
    padding-top: 3rem;
    padding-bottom: 1rem;
    .footerCpy{
      width: 100%;
      display: inline-block;
      margin-top: 2rem;
      >p{
        color:#ecccd3;
        font-size: 1.2rem;
        text-align: center;
        img{
          height: 2rem;
          display: inline-block;
          margin-left: 1rem;
          vertical-align: middle;
        }
      }
    }
    .footerAccept{
      width: 100%;
      >p{
        font-size: 1.4rem;
        color:#fff;
        text-align: center;
        margin-bottom: 1rem;
      }

      >div {
        text-align: center;
        img {
          height: 2.5rem;
          margin: 0.3rem 0.5rem;
        }
      }
    }
    .footerNav{
      width: 100%;
      margin: 0 auto;
      margin-top: 1rem;
      margin-bottom: 2rem;
    }
    .footerLogo {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      img{
        width: 70%;
      }
    }
  }
}
</style>
