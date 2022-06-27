<template>
<div class="in_panel_header">
    <div class="in_panel_product">
        <div class="ProductCode">
            <div class="leftpart">{{panelDetail.Name}}</div>
            <div class="rightpart" v-if="FrontE.version !== 1"><HkProductShare></HkProductShare>{{$t("Action.Share")}}</div>
        </div>
    </div>
    <div class="in_panel_subTitle"  v-if="isPtx===false"><inPrices :primePrices="panelDetail.ListPrice+AddPrice" :currentPrices="panelDetail.SalePrice+AddPrice"  :currency="panelDetail.Currency" :DefaultListPrice="panelDetail.DefaultListPrice+AddPrice" :DefaultSalePrice="panelDetail.DefaultSalePrice+AddPrice" :DefaultCurrency="panelDetail.DefaultCurrency" size="huge" :heightLine="true" styla="margin: 1rem 0;" :max="panelDetail.MaxPurQty" :min="panelDetail.MinPurQty"></inPrices></div>
    <div class="in_pannel_addtofav"  v-if="isPtx===false"><img :src="panelDetail.IsFavorite ? '/images/pc/productDetail_01.png': '/images/pc/productDetail_05.png'" @click="addFavorite"/></div>
    <div class="productInfo">
      <p class="TitleBg"><span>{{$t('Message.ProductInformation')}}</span></p>
      <div class="InnerTable">
        <p class="perline"><span class="left">{{$t('Enquiry.MinOrderQty')}}</span><span class="right">{{panelDetail.MinPurQty}}</span></p>
        <p class="perline"><span class="left">{{$t("product.ProductCode")}}</span><span class="right">{{panelDetail.Code}}</span></p>
        <p class="perline"><span class="left">{{$t('Message.Catalog')}}</span><span class="right">{{panelDetail.CatPathName}}</span></p>
      </div>
    </div>
     <div class="productInfo">
        <p class="TitleBg"><span>{{$t('Message.OtherDetails')}}</span></p>
        <div class="InnerTable">
          <p class="perline"><span class="left">{{$t('Message.Remark')}}</span><span class="right">1(pcs)</span></p>
        </div>
    </div>
    <div class="in_unitInfo" v-if="panelDetail.UnitInfo.Desc!==null">{{$t('product.Unit')}}:{{panelDetail.UnitInfo.Desc}}</div>

</div>
</template>
<script lang="ts">
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import PanelDetail from '@/model/PanelDetail';
import inPrices from '@/components/base/mobile/InsPrices.vue';
import HkProductShare from '@/components/hkTasteBusiness/mobile/product/HkProductShare.vue';
@Component({ components: { inPrices, HkProductShare } })
export default class PkProductInfo extends Vue {
  @Prop() private readonly panelDetail!: PanelDetail;
  @Prop() private readonly ProductSku!: string;
  @Prop() private readonly AddPrice!: string;
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  addFavorite () {
    let ps;
    if (this.panelDetail.IsFavorite) {
      ps = this.$Api.product.removeFavorite(this.ProductSku).then((result) => {
        if (result.Succeeded) {
            this.$message({
              message: this.$t('product.successInRemoving') as string,
              type: 'success',
              customClass: 'messageBoxMobile'
            });
          this.panelDetail.IsFavorite = false;
        } else if (result.ReturnValue.Code === 1000) {
          Vue.prototype.$Confirm(this.$t('product.logouted'), this.$t('product.loginow'), () => { this.$Login(this.addFavorite); });
        }
      });
    } else {
      ps = this.$Api.product.addFavorite(this.ProductSku).then((result) => {
        if (result.Succeeded) {
          // Vue.prototype.$Confirm(this.$t('Message.Message'), this.$t('product.successInAdding'));
          this.$message({
            message: this.$t('product.successInAdding') as string,
            type: 'success',
            customClass: 'messageBoxMobile'
          });
          this.panelDetail.IsFavorite = true;
        } else if (result.ReturnValue.Code === 1000) {
          Vue.prototype.$Confirm(this.$t('product.logouted'), this.$t('product.loginow'), () => { this.$Login(this.addFavorite); });
        }
      });
    }
    return ps;
  }
}
</script>
<style lang="less">
.messageBoxMobile{
      z-index: 100000!important;
}
</style>
<style scoped lang="less">
.in_unitInfo{
  font-size: 1.2rem;
  color:@base_color;
  text-align: right;
  width: 95%;
  margin: 0 auto;
}
.in_panel_header{
  width: 100%;
  display: block;
}
.in_panel_product{
    width: 90%;
    margin: 0 auto;
    margin-top: 1rem;
    margin-bottom: 1rem;
    display: flex;
    flex-wrap: wrap;
}
.in_pannel_addtofav{
    width: 95%;
    margin: 0 auto;
    text-align: center;
}
.in_pannel_addtofav img{
    width:2.5rem;
}
.in_panel_product .ProductCode{
    width: 95%;
    margin: 0 auto;
}
.in_panel_product .ProductCode .leftpart{
    width:75%;
    float: left;
    font-size: 1.4rem;
    word-break: break-all;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    padding-top: .5rem;
}
.in_panel_product .ProductCode .rightpart{
    width: 23%;
    float: left;
    text-align: right;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    font-size: 1rem;
}
.productInfo {
  width: 100%;
  display: inline-block;
  margin-bottom: 1rem;
  .TitleBg {
    height: 3rem;
    line-height: 3rem;
    background: #cab597;
    span {
      width: 90%;
      margin: 0 auto;
      font-size: 1.4rem;
      display: flex;
      flex-wrap: wrap;
      color: #fff;
    }
  }
  .InnerTable {
    width: 90%;
    margin: 0 auto;
    .perline {
      width: 100%;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      margin-top: 1rem;
      .left{
        width: 48%;
        font-size: 1.2rem;
      }
      .right {
        width: 48%;
        font-size: 1.2rem;
      }
    }
  }
}
.in_panel_subTitle{
    font-size: 2rem;
    position: relative;
    width: 90%;
    margin: 0 auto;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
  >img{
    position: absolute;
    right: 0;
    top: 50%;
    transform: translate(0,-50%);
  }
}
</style>
