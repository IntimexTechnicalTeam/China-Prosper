<template>
  <div class="PromotionMain">
       <div class="productCat">
          <p class="NoramlTitle"><span class="text">{{productCatName}}</span></p>
          <swiper  :options="productCatOption" ref="mySwiper" v-if="productCatData.length > 0">
            <!-- slides -->
            <swiper-slide v-for="(slide, index) in productCatData" :key="index">
              <a :href="'/product/search/-?' + 'catalogs=' + JSON.stringify([slide.Id]) + '&type=0'" :target="slide.Url ? slide.IsRedirect ? '_blank' : '_self' : ''" class="borderInner">
                <span class="bgTitle"><img :src="slide.Img" /></span>
                <p class="TitleName">{{slide.Name}}</p>
              </a>
            </swiper-slide>
            <div class="swiper-pagination" slot="pagination" v-if="productCatOption.pagination"></div>
          </swiper>
       </div>
       <div class="nestContent">
          <div class="InnerWhite">
            <div class="Innerbg">
              <p class="NoramlTitle"><span class="text">{{nestContent.Title}}</span></p>
                <div class="ContentText">
                  <p v-html="nestContent.Body"></p>
                  <p class="learnMore"><router-link to="/cms/catDetail/40113">{{$t('Message.LearnMore')}}>></router-link></p>
                </div>
            </div>
          </div>
       </div>
       <div class="BottomMain">
          <div class="News">
              <p class="NoramlTitle"><span class="text">{{$t('Message.News')}}</span></p>
              <div class="NewsMain">
                <ul>
                  <li v-for="(v,index) in NewsData" :key="index" @click="goClick(v)">
                      <span class="Title">{{v.Title}}</span>
                      <span class="Date">{{v.ContentDateTime}}</span>
                  </li>
                </ul>
              </div>
          </div>
          <div class="Contact">
            <p v-html="MapInfo" class="Map"></p>
            <p v-html="Contact.Body" class="contactContent"></p>
          </div>
       </div>
  </div>
</template>
<script lang="ts">
import { Vue, Prop, Component } from 'vue-property-decorator';
import { swiper, swiperSlide } from 'vue-awesome-swiper/src';
@Component({
  components: {
    HkHotProduct: () => import('@/components/hkTasteBusiness/mobile/home/HkHotProduct.vue'),
    swiper,
    swiperSlide
  }
})
export default class HkPromotion extends Vue {
  productCatData:any[]=[];
  productCatName:string='';
  nestContent:any[]=[];
  currentPage:number=1;
  pageSize:number =5;
  totalRecord:number=0;
  NewsData:any[]=[];
  Contact:any[]=[];
  MapInfo:any[]=[];
  NewsTitle:string='';
  goClick (v) {
    window.location.href = '/cms/contentN/' + v.Id;
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  productCatOption: any = {
    slidesPerView: 2,
    spaceBetween: 20,
    observer: true, // 修改swiper自己或子元素时，自动初始化swiper
    observeParents: true, // 修改swiper的父元素时，自动初始化swiper
    pagination: {
      el: '.swiper-pagination',
      clickable: true
    }
  };
    getProductCat () {
    // 获取产品详情数据
    this.$Api.product.getAttrList2().then((result) => {
      if (result) {
        this.productCatData = result.Catalogs[0].Children;
        this.productCatName = result.Catalogs[0].Name;
      }
    });
  }
  getNewsContents () {
      var pas = {
        Key: 'NewsData',
        currentPage: this.currentPage,
        pageSize: this.pageSize,
        SortName: 'ContentDateTime',
        SortOrder: 'Desc'
      };
      this.$Api.cms.getContentsByCatKeyEx(pas).then((result) => {
        this.NewsData = result.Data;
        result.Data.forEach(function (i) {
        var newDate = new Date(i.ContentDateTime.replace(/-/g, '/'));
        i.ContentDateTime = newDate.getFullYear() + '-' + (newDate.getMonth() + 1) + '-' + newDate.getDate();
        });
      });
    }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: 'HomeNest', IsMobile: true }).then(result => {
      this.nestContent = result.CMS;
    });
  }
  getContact () {
    this.$Api.cms.getContentByDevice({ Key: 'contactus', IsMobile: true }).then(result => {
      this.Contact = result.CMS;
      console.log(this.Contact, 'this.Contactthis.Contact');
      this.getCategoryByDevice(result.CMS.CatId);
    });
  }
  // 根据设备类型获取CMSCategory信息
  getCategoryByDevice (cateId) {
    this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: true }).then(async (result) => {
      this.MapInfo = result.Content;
      console.log(this.MapInfo, 'this.MapInfo ');
    }).catch((error) => {
      console.log(error, 'error');
      this.$message({
        message: error,
        type: 'error'
      });
    });
  }

  created () {
    this.getProductCat();
    this.getContent();
    this.getNewsContents();
    this.getContact();
  }
}
</script>
<style lang="less" scoped>
.PromotionMain{
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    position: relative;
    .NoramlTitle {
      background: url('/images/mobile/ptx_14.png') no-repeat center center;
      background-size: contain;
      display: flex;
      flex-wrap: wrap;
      position: relative;
      width: 60%;
      height: 3rem;
      margin: 0 auto;
      justify-content: center;
      align-items: center;
      .text {
        font-size: 1.6rem;
        color: #fff;
        padding-left: 3rem;
        font-weight: 700;
      }
    }
    .nestContent {
      background: url('/images/mobile/ptx_16.jpg') no-repeat center center;
      background-size: cover;
      width: 100%;
      display: flex;
      flex-wrap: wrap;
      padding-top: 2rem;
      padding-bottom: 2rem;
      .InnerWhite {
        width: calc(90% - 2rem);
        margin: 0 auto;
        padding: 1rem;
        background: #fff;
        .Innerbg {
          background: #fff url('/images/mobile/ptx_24.png') no-repeat center center;
          background-size: 100% 100%;
          width: calc(100% - 3rem);
          padding: 1.5rem;
          position: relative;
          .NoramlTitle {
            position: absolute;
            top: 0px;
            left: 50%;
            transform: translate(-50%);
          }
          .ContentText {
            padding-top: 4rem;
            .learnMore {
              width: 100%;
              display: flex;
              justify-content: center;
              align-items: center;
              margin-top: 1rem;
              margin-bottom: 1rem;
              a {
                width: 80%;
                padding-top: 5px;
                padding-bottom: 5px;
                text-align: center;
                border-radius:2px;
                border:1px solid @base_color;
                color: @base_color;
                font-size: 1.2rem;
              }
            }
            /deep/ p {
              font-size: 1.2rem;
              line-height: 2rem;
              span{
                font-size: 1.2rem;
                 line-height: 2rem;
              }
            }
          }
        }
      }
    }
    .productCat {
      width: 100%;
      display: flex;
      flex-wrap: wrap;
      background: url('/images/mobile/ptx_17.jpg') no-repeat center center;
      background-size: cover;
      padding-top: 2rem;
      padding-bottom: 2rem;
      /deep/ .swiper-container {
        padding-bottom: 3rem;
        padding-top: 2rem;
        width: 90%;
        margin: 0 auto;
        padding-left: 1rem;
        padding-right: 1rem;
        .swiper-pagination-bullet{
          width: 10px!important;
          height: 10px!important;
          background: #e6e6e6;
          opacity: 1;
        }

        .swiper-pagination-bullet-active{
          background: #8f1121!important;
        }
        .borderInner {
          overflow: hidden;
          display: flex;
          flex-wrap: wrap;
          width: 100%;
          justify-content: center;
          position: relative;
        }
        img {
          width: 100%;
          border: 1px solid #d9c5a8;
          border-radius: 100%;
          transition: all .3s;
        }
        .bgTitle{
          background: url(/images/pc/pcptx_12.png) no-repeat center center;
          background-size: contain;
          overflow: hidden;
          display: inline-block;
          padding: 2rem;
        }
        .TitleName{
          color: #333333;
          display: flex;
          flex-wrap: wrap;
          width: 100%;
          align-items: center;
          justify-content: center;
          font-size: 20px;
          margin-top: 10px;
          transition: all .3s;
          font-weight: 700;
        }
      }
    }
    .BottomMain {
        background: #fff url('/images/mobile/ptx_17.jpg') no-repeat center center;
        background-size: 100% 100%;
        width: 100%;
        display: flex;
        flex-wrap: wrap;
        padding-top: 2rem;
        padding-bottom: 2rem;
        .News {
          width: 90%;
          margin: 0 auto;
          display: flex;
          flex-wrap: wrap;
          .NewsMain {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            ul{
              width: 100%;
              display: flex;
              flex-wrap: wrap;
              align-items: center;
              li{
                width: 100%;
                display: flex;
                flex-wrap: wrap;
                align-items: center;
                justify-content: space-between;
                padding-bottom: 1rem;
                padding-top: 1rem;
                border-bottom: 1px solid #e6ccb1;
                &:nth-child(1) {
                  padding-top: 2rem;
                }
                span{
                  font-size: 1.2rem;
                }
                .Title {
                  color:#333333;
                  display: flex;
                  align-items: center;
                  width: 50%;
                  &::before {
                    content:'';
                    width: 5px;
                    height:5px;
                    border-radius: 5px;
                    display: inline-block;
                    background: #e6ccb1;
                    margin-right: 5px;
                  }
                }
                .Date {
                  color:#999999;

                }
              }
            }
          }
        }
        .Contact {
          width: 100%;
          display: flex;
          flex-wrap: wrap;
          margin-top: 2rem;
          .Map {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            /deep/ p{
              width: 100%;
              display: flex;
              flex-wrap: wrap;
              img {
                width: 100%;
              }
            }
          }
          .contactContent {
            width: 90%;
            margin: 0 auto;
            /deep/ .contatctBox {
              width: 100%;
              display: flex;
              flex-wrap: wrap;
              .PerList {
                width: 100%;
                display: flex;
                flex-wrap: wrap;
                margin-top: 2rem;
                .NormalText {
                  font-size: 1.4rem;
                  color: #333333;
                  width: 100%;
                  display: flex;
                  flex-wrap: wrap;
                  align-items: center;
                  justify-content: center;
                  text-align: center;
                  line-height: 2rem;

                }
                .HeadText {
                  width: 100%;
                  display: flex;
                  flex-wrap: wrap;
                  align-items: center;
                  justify-content: center;
                  .Text {
                    font-size: 1.4rem;
                    color: #9f1e3c;
                    font-weight: 700;
                  }
                  .IconA {
                    width: 2.5rem;
                    height: 2.5rem;
                    background: url('/images/mobile/ptx_23.png') no-repeat center center;
                    display: flex;
                    background-size: contain;
                  }
                  .IconB {
                    width: 2.5rem;
                    height: 2.5rem;
                    background: url('/images/mobile/ptx_11.png') no-repeat center center;
                    display: flex;
                    background-size: contain;
                  }
                  .IconC {
                    width: 2.5rem;
                    height: 2.5rem;
                    background: url('/images/mobile/ptx_13.png') no-repeat center center;
                    display: flex;
                    background-size: contain;
                  }
                  .IconD {
                    width: 2.5rem;
                    height: 2.5rem;
                    background: url('/images/mobile/ptx_15.png') no-repeat center center;
                    display: flex;
                    background-size: contain;
                  }
                }
              }
            }
          }
        }
    }

}
</style>
