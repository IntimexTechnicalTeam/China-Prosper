<template>
 <div class="accountHeader">
   <div class="accountBg">
      <p class="memberCenterTitle">{{$t('Account.MemberCenter')}}</p>
      <p class="accountUser">{{MemberName}}</p>
      <div class="accountMeun">
          <p><router-link to="/account/memberInfo">{{$t('Account.MemberInformation')}}</router-link></p>
          <p><router-link to="/order/List">{{$t('Account.MyOrder')}}</router-link></p>
          <p><router-link to="/account/deliveryAddress">{{$t('Account.DeliveryAddress')}}</router-link></p>
          <p><router-link to="/account/modifyPassword">{{$t('MemberInfo.ModifyPassword')}}</router-link></p>
          <p><router-link to="/account/notification">{{$t('Account.MyMessages')}}</router-link></p>
          <p><router-link to="/account/myFavorite">{{$t('Account.MyFavorite')}}</router-link></p>
          <p><router-link to="/account/myCoupon">{{$t('MyCoupon.MyCoupon')}}</router-link></p>
          <p><router-link to="/account/message">{{$t('Account.LatestNews')}}</router-link></p>
          <p><router-link to="/account/ptxorder">{{$t('Enquiry.PTXOrder')}}</router-link></p>
      </div>
   </div>
 </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
import sdk from '@/sdk/InstoreSdk';
@Component
export default class accountHeader extends Vue {
   MemberName:string='';
   TotalPoints:string='';
   // 获取会员信息
   getMemberInfo () {
     let _this = this;
     sdk.api.member.getProfile().then(
       function (data) {
         console.log(data, 'datadata');
         _this.MemberName = data.FirstName + '  ' + data.LastName;
       }
     );
   }
   // 获取会员总积分
   getMemberTotal () {
     this.$Api.pointsApi.GetMemberTotalPointsInfo().then((result) => {
       this.TotalPoints = result.data.TotalPoints;
     });
   }
   get currentlang () {
     return this.$Storage.get('locale');
   }
   mounted () {
     this.getMemberInfo();
     this.getMemberTotal();
   }
}
</script>

<style scoped lang="less">
/*頁面中間目錄*/
ul,li{
    list-style: none;
}
.clear{
    clear: both;
}
.Emeun {
  p{
    img{
      width: 100%;
    }
  }
}
.accountHeader{
  width: 100%;
  display:inline-block;
  padding-top: 4rem;
  .memberCenterTitle{
    font-size: 2rem;
    text-align: center;
    display: block;
    padding-top: 2rem;
    color:#fff;
    text-transform:uppercase;
  }
  .accountBg{
    width:100%;
    display: inline-block;
    box-sizing: border-box;
    padding-bottom: 3rem;
    background: url('/images/mobile/ptx_16.jpg') no-repeat center center;
    background-size: cover;
    .accountMeun{
      width: 90%;
      margin: 0 auto;
      p{
        display: block;
        height: 3.5rem;
        margin-bottom: 1.5rem;
        a{
          background: #fff;
          display: flex;
          width: 100%;
          align-items: center;
          justify-content: center;
          color:#333;
          border-radius: 20px;
          font-size: 1.6rem;
          height: 100%;
          text-transform:uppercase;
        }
        .router-link-active {
          background: @base_color!important;
          color: #fff!important;
        }
      }
    }
    .accountUser{
      font-size: 2rem;
      text-align: center;
      color:#fff;
      padding-top: 2rem;
      padding-bottom: 2rem;
    }
    .accountTop{
      width: 70%;
      margin: 0 auto;
      img{
        width: 100%;
      }
    }
  }
}

</style>
