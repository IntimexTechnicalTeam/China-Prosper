<template>
  <div id="container" class="MobileContact">
    <div class="CmsNormal">
      <div class="CmsContent">
          <p class="textTitle">{{content.Title}}</p>
          <p v-html="content.Body"></p>
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
  }
})
export default class InsCmsContentN extends Vue {
  content: any[] = [];
  get currentlang () {
    return this.$Storage.get('locale');
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: true }).then(result => {
      this.content = result.CMS;
      if (result.CMS.Title) document.title = result.CMS.Title;
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  get queryLang () {
    return this.$route.query.Lang || '';
  }
  created () {
    this.getContent();
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    this.getContent();
  }
}
</script>
<style scoped lang="less">
.clear {
  clear: both;
}
.CmsNormal {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  .CmsContent {
    width: 90%;
    margin: 0 auto;
    padding-top: 5rem;
    padding-bottom: 5rem;
    min-height: 15rem;
    .textTitle {
      font-size: 1.8rem;
      color: #666666;
      line-height: 2rem;
      display: flex;
      align-items: center;
      margin-bottom: 2rem;
      &::before {
        content: '';
        height: 2rem;
        width: 3px;
        background: @base_color;
        display: inline-block;
        margin-right: .5rem;
      }
    }
    /deep/ p {
      font-size: 1.2rem;
      line-height: 2rem;
      color: #666666;
      word-break: break-word;
    }
    /deep/ strong {
      font-size: 1.2rem;
      line-height: 2rem;
      color: #666666;
      font-weight: 700;
      word-break: break-word;
    }
  }
}
</style>
