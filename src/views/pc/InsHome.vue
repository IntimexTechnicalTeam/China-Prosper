<template>
  <div class="home" v-cloak>
    <HomeBanner :page="'Home'" />
    <HkPromotion/>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component({
  components: {
    HomeBanner: () => import('@/components/base/pc/InsBanner.vue'),
    HkPromotion: () => import('@/components/hkTasteBusiness/pc/home/HkPromotion.vue')
  }
})
export default class InsHome extends Vue {
  swiperOption: object = {
    autoplay: {
      disableOnInteraction: false
    },
    pagination: {
      el: '.swiper-pagination',
      clickable: true
    }
  };

  get AreaCode () {
    return localStorage.getItem('AreaCode') || '';
  }
}
</script>

<style lang="less" scoped>
/deep/ .banner {
  .swiper-pagination-bullet{
    width: 10px!important;
    height: 10px!important;
    background: #e6e6e6;
    opacity: 1;
  }

  .swiper-pagination-bullet-active{
    background: #8f1121!important;
  }
}

.activity {
  width: 90%;
  margin: 5rem auto;

  .TitleBg{
    width: 90%;
    height: 5rem;
    border:1px solid #4d4d4d;
    margin: 0 auto;
    padding: .5rem;
    margin-bottom: 4rem;
    .innerBox{
      width: 100%;
      height: 100%;
      background:#4d4d4d;
      color: #FFF;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 2rem;
      font-weight: 700;
      font-family: 'Arial';
    }
  }
}

.ins-calendar {
  margin: 2rem auto;
}
</style>
