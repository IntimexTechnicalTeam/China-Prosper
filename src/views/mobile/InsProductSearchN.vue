<template>
  <div id="container" class="ProductSearch  NomralBg">
<!--     <div class="NsMain"  v-if="isPtx">
        <advancedSearch :attrType="2"  @advancedChange="advancedChange" />
        <div class="ProductTips">
          <p>{{$t('Message.TipsA')}}</p>
          <p>{{$t('Message.TipsB')}} <span> {{totalRecord}} </span>{{$t('Message.TipsC')}} </p>
          <p class="redcolor"><router-link to="/account/login">{{$t('Message.LoginNow')}},</router-link><span class="bcolor">{{$t('Message.TipsD')}} </span></p>
          <p>{{$t('Message.TipsE')}} {{totalRecord}} </p>
        </div>
    </div> -->
    <div class="NsMain">
        <div class="SearchSlide">
          <div class="leftSide">
            <NsadvancedSearch @advancedChange="advancedChange" v-if="isAdvanced"  @closeSub="closeSub" @resetAll="resetAll" />
          </div>
        </div>
      <div class="selectBar">
          <ul>
            <li @click="showSearchSlide"><span class="filterIcon"></span><b>{{$t('product.Screening')}}</b></li>
            <li  class="sortBox">
              <p class="sortTitle" @click.stop="showList=!showList">
                {{$t('product.SortBy')}}
                <i class="el-icon-arrow-down el-icon--right"></i>
              </p>
              <transition name="el-fade-in-linear">
                <ul class="sortList" v-if="showList">
                  <li @click="handleCommand('desc')" :style="{'color':command=='desc'?'#b19162':'#333333'}">{{$t('product.PriceHL')}}</li>
                  <li @click="handleCommand('asc')" :style="{'color':command=='asc'?'#b19162':'#333333'}">{{$t('product.PriceLH')}}</li>
                </ul>
              </transition>
            </li>
             <li  class="sortBox liTop" @click.stop="ShowSellType=!ShowSellType">
              <p class="sortTitle">
                {{$t('Enquiry.SellType')}}
                <i class="el-icon-arrow-down el-icon--right"></i>
              </p>
              <transition name="el-fade-in-linear">
                <ul class="sortList" v-if="ShowSellType">
                  <li @click="handleSellType(0)" :style="{'color':SellType==0?'#b19162':'#333333'}">{{$t('Enquiry.RetailBargaining')}}</li>
                  <li @click="handleSellType(1)" :style="{'color':SellType==1?'#b19162':'#333333'}">{{$t('Enquiry.Retail')}}</li>
                  <li @click="handleSellType(2)" :style="{'color':SellType==2?'#b19162':'#333333'}">{{$t('Enquiry.Bargaining')}}</li>
                </ul>
              </transition>
            </li>
          </ul>
        </div>
    </div>
    <div class="prolist-box">
      <div class="products_container" v-if="proList.length>0">
          <InsProductList v-for="item in proList" :key="item.productCode" :item="item" :needCode="false" class="product_item" ></InsProductList>
        </div>
        <div class="products_container" v-else>
             <h3 class="nocontentTips">{{$t('messageTips.NoContent')}}</h3>
        </div>
          <div class="pager" v-if="totalRecord > pageSize">
            <InsPage
              :total="totalRecord"
              v-model="currentPage"
              :pageNum="pageSize"
              :currentPage = "currentPage"
            ></InsPage>
          </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import YouWouldLike from '@/model/youWouldLike';
import $ from 'jquery';
@Component({
  components: {
    InsProductList: () => import('@/components/hkTasteBusiness/mobile/product/HkProductWindow.vue'),
    advancedSearch: () => import('@/components/hkTasteBusiness/mobile/product/InsAdvancedSearch.vue'),
    NsadvancedSearch: () => import('@/components/hkTasteBusiness/mobile/product/NsAdvancedSearch.vue'),
    ProductListSwiper: () => import('@/components/hkTasteBusiness/mobile/product/HkProductListSwiper.vue'),
    InsPage: () =>
      import(
        /* webpackChunkName: 'product' */ '@/components/base/mobile/InsPage.vue'
      )
  }
})
export default class InsProductSearch extends Vue {
  proList: YouWouldLike[] = []; // 产品数据
  currentPage: number = 1; // 当前页
  pageSize: number =12; // 每页显示条目个数
  totalRecord: number = 0;// 总条目数
  private tips:boolean = true;
  private LoadingInstance!: any;

  attrs: object[] = []; // 选中的产品属性数组
  searchCatalogs: number[] = []; // 选中的产品目录数组
  searchType: number = 1; // 搜索类型（0 => 叠加，1 => 筛选）
  PriceItem:string='desc';
  isAdvanced: boolean = true;
  showList:boolean = false;
  command:string='';
  SortName:string = '';
  SellType:number=0;
  ShowSellType:boolean =false
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  // 搜索关键词
  get searchKey () {
    let a = this.$store.state.searchKey;
    if (a === '-' || a === '') {
      return '';
    } else {
      return a;
    }
  }
  get islogin () {
    return this.$Storage.get('isLogin');
  }
  // 价格传值
  getselect (val) {
    this.PriceItem = val;
    this.productSearch();
  }
  handleCommand(command) {
    this.command = command;
    if (command === 'newest') {
      this.SortName = 'createdate';
      this.PriceItem = 'desc';
      this.showList = false;
    } else {
      this.SortName = 'SalePrice';
      this.PriceItem = command;
      this.showList = false;
    }

    this.productSearch();
  }
  handleSellType(command) {
    this.SellType = command;
    this.productSearch();
  }
  // 产品高级搜索
  // 产品高级搜索
  productSearch() {
    this.TShake(() => {
      this.$Api.product
        .search({
          Key: this.searchKey,
          PageInfo: {
            Page: this.currentPage,
            PageSize: this.pageSize,
            SortName: this.SortName,
            SortOrder: this.PriceItem
          },
          CatIdS: this.searchCatalogs,
          Attrs: this.attrs,
          Type: this.searchType,
          Reflesh: 1
        })
        .then(result => {
          this.proList = result.YouWouldLike;
          this.totalRecord = result.TotalRecord;
          this.$HiddenLayer();
        });
    }, 500);
  }
  delay = 0;
  TShake(fn, d) {
    if (!(fn instanceof Function)) return;
    let timeout = d || 200;
    if (this.delay === 0) {
      this.delay = setTimeout(fn, timeout);
    } else {
      clearTimeout(this.delay);
      this.delay = setTimeout(fn, timeout);
    }
  }
  advancedChange (Attrs, Catalogs) {
    this.currentPage = 1;
    this.attrs = Attrs;
    this.searchCatalogs = Catalogs;
    this.productSearch();
  }
  // 重置搜索
  resetAll () {
    window.location.href = '/product/search/-';
    this.closeSub();
  }
  // 关闭筛选弹窗
  closeSub () {
    $('.SearchSlide').fadeOut();
    $('.leftSide').removeClass('closeBar');
    document.body.style.overflow = 'auto';
  }
  // 打开筛选弹窗
  showSearchSlide () {
    $('.SearchSlide').fadeIn();
    $('.leftSide').addClass('closeBar');
    document.body.style.overflow = 'hidden';
  }
  loading (e) {
    if (this.tips) {
      this.LoadingInstance = this.$loading({
        target: this.$refs.load as any,
        fullscreen: false,
        // spinner: 'el-icon-loading',
        text: 'Loading...'
      });
      setTimeout(() => {
        this.load();
        this.LoadingInstance.close();
      }, 2000);
    }
  }
  load () {
    console.log(this.totalRecord, this.proList.length);
    if (this.totalRecord !== this.proList.length) { this.currentPage++; } else { this.tips = false; }
  }

  mounted () {

  }

  @Watch('searchKey', { deep: true })
  onSearchKeyChange () {
    this.productSearch();
  }
  @Watch('currentPage', { deep: true })
  onCurrentPage() {
    this.productSearch();
  }
}
</script>

<style lang="less">
.ProductSearch {
  .el-loading-spinner .circular {
    display: none;
  }

  .el-loading-text {
    font-size: 1.3rem;
    color: #cccccc;
    font-family: 'Arial';
  }

  .el-loading-parent--relative {
    > p {
      display: none;
    }
  }
}
</style>

<style scoped lang="less">
.NsMain {
  width: 100%;
  display: inline-block;
}
.sortBox{
  position: relative;
  z-index: 1;
  .sortTitle{
    width: 100%;
    height: 3rem;
    font-size: 1.4rem;
    color: #b19162;
    text-align: center;
    img{
      margin-left:13%;
      margin-right:10%;
      width:10px;
    }
  }
  .sortList{
    width:100%;
    box-shadow: 0px 2px 5px #ccc;
    position: absolute;
    left:0;
    top:4rem;
    box-sizing:border-box;
    background: #fff;
    li{
      text-align: center;
      border-bottom: 1px solid #eee;
      font-size: 1.2rem;
      &:last-of-type{
        border-bottom: none;
      }
    }
  }
}
.ProductTips {
  width: 90%;
  margin: 0 auto;
  p{
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    font-size: 1.2rem;
    align-items: center;
    margin-top: 10px;
    span{
      font-size: 1.2rem;
    }
    a{
      font-size: 1.2rem;
    }
  }
  .redcolor {
    color:#9f1e3c;
    a{
      color:#9f1e3c;
    }
  }
}
.pager {
  width: 90%;
  margin: 0 auto;
}
.prolist-box {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
}
.ProductSearch {
  padding-top: 6rem;
  padding-bottom: 6rem;
}
.nocontentTips{
  width: 95%;
  margin: 0 auto;
  font-size: 1.4rem;
  font-weight: 500;
  text-align: left;
  display: block;
  padding: 1rem;
  color: #666;
}
.product_warpper{
  width: 100%;
  margin:0 auto;
}
.products_container{
  display: flex;
  flex-wrap: wrap;
  width: 90%;
  margin: 0 auto;
  margin-top: 20px;
}

.product_item{
  width: 48%;
  margin-right: 4%;
  margin-bottom: 4%;
  &:nth-child(2n) {
    margin-right: 0px!important;
  }
}

.loading{
    text-align: center;
    height: 3rem;
    line-height: 3rem;
    margin: 1rem 0 2rem;
}

.SearchSlide{
  width: 100%;
  position: fixed;
  left: 0;
  top: 0px;
  bottom: 0px;
  background: rgba(0,0,0,.7);
  overflow-x: auto;
  z-index: 999999;
  display: none;
  .leftSide{
    width: 70%;
    left:-70%;
    min-height: 100%;
    position: absolute;
    transition: all .5s;
    background: #f2f1f0;
    border-top-right-radius: 1rem;
  }

}
.closeBar{
    left: 0%!important;
  }
.selectBar{
    width: 100%;
    margin: 0 auto;
    display: inline-block;
    margin-top: 2rem;
  > ul{
    width: 95%;
    margin: 0 auto;
  > li{
    float: left;
    margin-right: 3%;
    width: 30%;
    background: #FFF;
    border:1px solid #eee;
    font-size: 1.6rem;
    background: url('/images/mobile/filterbg.png') no-repeat center center;
    background-size: 100% 100%;
    color: #FFF;
    height: 4rem;
    line-height: 4rem;
    list-style: none;
    span{
    width: 20%;
    display: inline-block;
    font-size: 1.4rem;
    text-align: center;
    }
    b{
      width: 60%;
      display: inline-block;
      text-align: center;
      font-size: 1.4rem;
      font-weight: 500;
      color: #b19162;
    }
    select{
    width: 100%;
    border: none;
    padding-left: .5rem;
    height: 3.5rem;
    line-height: 3.5rem;
    font-size: 1.2rem;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    background: url(/images/mobile/ptx_30.png) 80% 15px no-repeat;
    background-size: 15px;
    outline: none;
    color: #b19162;
    }
    &:last-child{
      margin-right: 0px!important;
    }
  }
  }
}
</style>
