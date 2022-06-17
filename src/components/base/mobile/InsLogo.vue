<template>
  <a href="/" class="logo">
    <img src="/images/mobile/ptx_04.png" />
  </a>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class InsLogo extends Vue {

}
</script>
<style scoped lang="less">
.logo {
  img {
    width: 15rem;
  }
}
</style>
