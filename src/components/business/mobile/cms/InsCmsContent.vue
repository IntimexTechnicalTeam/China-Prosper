<template>
  <div id="container" class="MobileContact NoramlPaddingTop">
    <!-- 联络我们页面 -->
    <div class="NomralBg" v-if="NewcateId=='40112'">
      <transition name="slide">
        <div key="1" v-if="!waiting" style="width:100%;">
           <div class="DetailTitle"><img :src="OtherPageImg" v-show="OtherPageImg!==null"><div class="TitleBg"><div class="innerBoxText">{{TitleName}}</div></div></div>
      </div>
      </transition>
      <transition name="slide">
        <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
      </transition>
      <div class="Contact">
        <p class="PathData"><router-link to="/" class="HomePath">{{$t('Message.HomeTips')}}</router-link><i class="el-icon-arrow-right"></i><span class="currentTitle">{{TitleName}}</span></p>
        <p v-html="content.Body" class="contactContent"></p>
        <p v-html="MapInfo" class="Map"></p>
      </div>
      <!-- 表单信息 -->
        <div class="FormMain">
          <div v-html="htmlString" class="to_vertical" id="content"></div>
          <div id="preview" style="display:none;"></div>
        </div>
    </div>
    <!-- 其他页面 -->
    <div class="CmsNormal" v-if="NewcateId!='40112'">
      <transition name="slide">
        <div key="1" v-if="!waiting" style="width:100%;">
              <div class="DetailTitle"><img :src="OtherPageImg" v-show="OtherPageImg!==null"><div class="TitleBg"><div class="innerBoxText">{{TitleName}}</div></div></div>
      </div>
      </transition>
      <transition name="slide">
        <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
      </transition>
      <div class="CmsContent NomralBg">
        <p class="PathData"><router-link to="/" class="HomePath">{{$t('Message.HomeTips')}}</router-link><i class="el-icon-arrow-right"></i><span class="currentTitle">{{TitleName}}</span></p>
        <p v-html="content.Body" class="contentText"></p>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { FrontE } from '@/sdk/common/SysConst';
import Cookie from 'js-cookie';
@Component({
  components: {
    PkcmsBanner: () => import('@/components/hkTasteBusiness/mobile/cms/PkcmsBanner.vue')
  }
})
export default class InsCmsContent extends Vue {
  NewsNav: string = 'NewsNav';
  CateName: string = '';
  CateDesc: string = '';
  content: any[] = [];
  FormContent:any='';
  private ImgList: string[] = [];
  private ispic:boolean=false;
  IsPay:boolean= false;
  IsLogin:boolean=false;
  IsMobile:boolean=true;
  MapInfo:string='';
  ShopList:any[]=[];
  cindex:number=0;
  private htmlString: string = '';
  Signer: any = null;
  FormTitle:string='';
  NewcateId:string='';
  private waiting: boolean = true;
  OtherPageImg:string='';
  TitleName:string='';
  get currentlang () {
    return this.$Storage.get('locale');
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }
  getForm () {
    this.$Api.regAndPay.getHtml('ContactUs', this.lang, false).then(result => {
      this.htmlString = result.HtmlString;
      this.FormTitle = result.Title;
      this.$nextTick(() => {
        if (document.querySelectorAll('#Sign').length > 0) {
          this.Signer = new intimex.CanvasSigner('#NewSignCanvas', '#Signature', {
            color: '#58B63A',
            width: 5
          });
          this.Signer.initCanvas();
          window['Signer'] = this.Signer;
        }
      });
    });
  }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: true }).then(result => {
      this.content = result.CMS;
      this.TitleName = result.CMS.Title;
      this.OtherPageImg = result.CMS.Cover;
      this.NewcateId = result.CMS.CatId;
      this.getCategoryByDevice(result.CMS.CatId);
      this.CateDesc = result.CMS.Desc;
      this.waiting = false;
      if (result.CMS.Title) document.title = result.CMS.Title;
    });
  }
  // 根据设备类型获取CMSCategory信息
  getCategoryByDevice (cateId) {
    this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: true }).then(async (result) => {
      this.MapInfo = result.Content;
    }).catch((error) => {
      console.log(error, 'error');
      this.$message({
        message: error,
        type: 'error'
      });
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  get queryLang () {
    return this.$route.query.Lang || '';
  }
  Regnay () {
    window['jsData'] = {
      HasPreview: true,
      UploadButtonText: this.$t('RegNPay.UploadButtonText'),
      UploadingText: this.$t('RegNPay.UploadingText'),
      UploadSuccessfulText: this.$t('RegNPay.UploadSuccessfulText'),
      UploadFailText: this.$t('RegNPay.UploadFailText'),
      NoFileText: this.$t('RegNPay.NoFileText'),
      UploadLengthText: this.$t('RegNPay.UploadLengthText'),
      UploadSizeText: this.$t('RegNPay.UploadSizeText'),
      BackText: this.$t('RegNPay.BackText'),
      ConfirmText: this.$t('RegNPay.ConfirmText'),
      PleaseSelect: this.$t('RegNPay.PleaseSelect'),
      PreviewTitleText: this.$t('RegNPay.PreviewTitleText'),
      RequiredText: this.$t('RegNPay.RequiredText'),
      FormatErrorText: this.$t('RegNPay.FormatErrorText'),
      Version: '2.0',
      HasRNPConfirm: false
    };
    this.$LoadScript('/static/js/CanvasSigner.js');
    this.$LoadScript('/static/js/ajaxFileUpload.js');

    document.dispatchEvent(new Event('rnpFinshed'));

    // RNP Form后台预览跳转语言判断
    if (this.queryLang) {
      this.$Api.member.setUILanguage(this.queryLang).then((result) => {
        this.$i18n.locale = this.queryLang as string;
        localStorage.setItem('locale', this.queryLang as string);
        this.getForm();
      }).catch((error) => {
        console.log(error);
      });
    } else {
      this.getForm();
    }
  }
  created () {
    this.getContent();
    this.Regnay();
  }
  mounted () {
    window['regAndPay'] = this.$Api.regAndPay;
    window['router'] = this.$router;
    window['Elalert'] = this.$alert;
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    this.getContent();
  }
}
</script>
<style lang="less">
.MobileContact .FormMain{
  #preview{
    width: 80%;
    float:right;
    .anwer{
      margin-bottom: 20px;
    }
    .back{
      background: #ccc;
      color:#FFF;
      padding:10px 20px 10px 20px;
      border:none;
      margin-right: 20px;
      margin-top: 30px;
    }
    .confirm{
      background: #333;
      color:#FFF;
      padding:10px 20px 10px 20px;
      border:none;
      margin-top: 30px;
    }
  }
}
.MobileContact .CmsMap .MapInfo{
  width:100%;
  margin-bottom: 1rem;
  iframe{
    width:100%;
    height: 30rem;
  }
  img{
    width:100%;
  }
}
.MobileContact .FormMain{
  width:90%;
  margin:0 auto;
  padding-bottom: 3rem;
  position: relative;
  padding-top: 3rem;
  .FormTitle{
    font-size: 2.5rem;
    margin-top: 2rem;
    margin-bottom: 2rem;
    color:#333333;
  }
  .FormImg{
    position: absolute;
    right: 0px;
    top:3rem;
    width: 20%;
    img{
      width: 100%;
    }
  }
  .form-group{
    .fieldset {
      border: none;
    }
    h4{
      background-size: 100% 100%;
      display: inline-block;
      width: 100%;
      height: 3rem;
      line-height: 3rem;
      font-size: 1.2rem;
      color: #666666;
      font-weight: 500;
    }
    input[type="text"],input[type="email"]{
      border:1px solid #eee;
      height: 3rem;
      line-height: 3rem;
      width: 100%;
      box-sizing: border-box;
      border-radius: 2px;
      margin-bottom: .5rem;
      text-indent: 1rem;
      outline: none;
      font-size: 1.4rem;
      border-radius: 5px;
      &:focus {
        border: 1px solid #9f1e3c;
      }
    }
    textarea{
      border:1px solid #eee;
      height: 10rem;
      width: 100%;
      box-sizing: border-box;
      border-radius: 2px;
      margin-bottom: .5rem;
      outline: none;
      font-size: 1.4rem;
      border-radius: 5px;
      &:focus {
        border: 1px solid #9f1e3c;
      }
    }
    p[name="error"]{
      color:red;
      margin-bottom:.5rem;
    }
    .btn-default{
      width: 100%;
      background: url('/images/mobile/ptx_08.jpg') no-repeat center center;
      background-size: cover;
      height: 3.5rem;
      line-height: 3.5rem;
      color:#fff;
      border:none;
      margin-top: 1rem;
      font-size: 1.6rem;
      margin-bottom: 5rem;
      border-radius: 5px;
      font-weight: 700;
    }
  }
}
</style>
<style scoped lang="less">
.TitleName{
  text-align: center;
  font-size: 1.6rem;
  font-weight: 700;
  margin-bottom: 2rem;
}
.DetailTitle{
  width: 100%;
  display: flex;
  flex-wrap:wrap;
  position: relative;
  align-items: center;
  justify-content: flex-start;
  img{
    width: 100%;
  }
  .TitleBg{
    width: 5rem;
    margin: 0 auto;
    margin-bottom: 20px;
    top: 10%;
    left: 10%;
    position: absolute;
    height: 12rem;
    background:url('/images/mobile/ptx_01.png') no-repeat center center;
    background-size: 100% 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    .innerBoxText{
      color: #fff;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 1.6rem;
      background-size: cover;
      text-align: center;
      width: 2rem;
      margin: 0 auto;
      padding-left: 1.5rem;
      font-weight: 700;
    }
  }
}
.CmsNormal{
  width: 100%;
  display: inline-block;
  background: #FFF;
}
.Banner {
  width: 100%;
  height: 15rem;
  display:flex;
  align-items: center;
  position: relative;
}
.Banner img {
  width: 100%;
  height: 15rem;
}
#container {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.CmsMap {
    width: 90%;
    margin: 0 auto;
    padding-top: 2rem;
}
.PathData {
  width: 90%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  margin-bottom: 1rem;
  span,a,i{
    font-size: 1.4rem;
  }
  .HomePath {
    color: #666;
  }
  .currentTitle {
    color:#9f1e3c;
  }
}
.CmsContent{
  width: 100%;
  margin-top: 2rem;
  .contentText {
    width: 90%;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
    /deep/ p{
      font-size: 1.4rem;
      line-height: 2rem;
      font-family: 'Arial', '宋体'!important;
      span {
        font-size: 1.4rem!important;
        line-height: 2rem!important;
        font-family: 'Arial', '宋体'!important;
      }
    }
    /deep/ .AboutMain {
        .BigImg {
          width: 100%;
          margin: 0 auto;
          margin-top: 20px;
          margin-bottom: 20px;
          img {
            width: 100%;
          }
        }
        .SmallImg {
          width: 100%;
          margin: 0 auto;
          margin-bottom: 20px;
          img {
            width: 100%;
          }
        }
      .VideoMain {
        width: 100%;
        display: inline-block;
        .perList {
          width: 100%;
          display: inline-block;
          margin-bottom: 20px;
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
    margin-top: 2rem;
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
.clear {
  clear: both;
}
</style>
