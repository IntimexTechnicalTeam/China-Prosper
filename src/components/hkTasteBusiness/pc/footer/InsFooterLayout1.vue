<template>
 <div id="footer" class="pcfooter">
    <div class="footer-box">
          <div class="footerNav">
              <ul v-for="(n,index) in footerMenus" :key="index">
                <li>
                    <a href="javascript:;" v-if="n.Type === 0" @click="toUrl(n)"><span>{{n.Name}}</span></a>
                    <router-link :to="To(n)"  v-else><span>{{n.Name}}</span></router-link>
                  <ul>
                    <li v-for="(c,index2) in n.Childs" :key="index2">
                       <a href="javascript:;" v-if="c.Type === 0" @click="toUrl(c)">
                              {{c.Name}}
                        </a>
                       <router-link :to="To(c)" v-else>{{c.Name}}</router-link>
                    </li>
                  </ul>
                </li>
             </ul>
          </div>
          <p class="footerLogo"><img src="/images/mobile/ptx_04.png"></p>
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
            <p>Copyright © {{currentYear}} Prosper Bird's Nest & Ginseng Co.Powered by Eventizer
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
    Menu: () => import('@/components/business/pc/header/InsElMenu.vue')
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
  background: url('/images/pc/pcptx_08.jpg') no-repeat center center;
  background-size: cover;
  border-top-left-radius: 10px;
  border-top-right-radius: 10px;
  .footer-box{
    width: 1200px;
    margin: 0 auto;
    padding-top: 1rem;
    padding-bottom: 1rem;
    .footerCpy{
      width: 100%;
      display: inline-block;
      margin-top: 1rem;
      >p{
        color:#ecccd3;
        font-size: 16px;
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
    width: 70%;
    margin: 0 auto;
    justify-content: space-between;
    display: flex;
}
.footerNav > ul{
    float: left;
    &:last-child {
      margin-right: 0px!important;
    }
}
.footerNav > ul >li{
    width: 100%;
    line-height: 20px;
}
.footerNav > ul >li >a{
    font-size:18px;
    color:#FFF;
}
.footerNav > ul >li >a >span{
    height: 40px;
   display: block;
}
.footerNav > ul >li >ul{
  width: 100%;
  display: none;
}
.footerNav > ul >li >ul a{
    font-size: 16px;
    color:#dbabb6;
    display: inline-block;
    padding-bottom: 10px;
}
.footerNav > ul >li >a:hover,.footerNav > ul >li >ul a:hover{
   text-decoration: underline;
   color: #fff;;
}
    .footerLogo {
      width: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      img{
        width: 200px;
      }
    }
  }
}
</style>
