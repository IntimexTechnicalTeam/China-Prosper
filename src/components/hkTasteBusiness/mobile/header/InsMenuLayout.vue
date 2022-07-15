<template>
    <div class="NoramlPaddingTop HeadMeun">
        <div class="header_logo" v-if="!this.FrontE.slideMenu.Embedded">
            <i class="el-icon-close" @click="closeSlideMenu"></i>
        </div>
        <div class="topLang">
          <div class="left"><InsLangSwitch/></div>
          <div class="right" v-if="!isPtx">
                <router-link to="/account/myFavorite" class="favImg"><img src="/images/mobile/unfav.png"></router-link>
                <InsCodeSelect class="codeSelect"/>
          </div>
        </div>
        <div class="search-box">
            <div class="search-input">
                <input type="text" v-model="key" @keyup.enter="search" />
                <span class="searchBtn" @click="search"><img src="/images/mobile/ptx_25.png"></span>
            </div>
        </div>

        <div id="menu">
            <Menu  :textColor="'#fff'" :uniqueOpened="true" />
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({
  components: {
    InsLogo: () => import('@/components/base/mobile/InsLogo.vue'),
    Menu: () => import('@/components/business/mobile/header/InsElMenu.vue'),
    InsLangSwitch: () => import('@/components/business/mobile/header/InsLangSwitch.vue'),
    InsCodeSelect: () => import('@/components/business/mobile/header/InsCodeSelect.vue')
  }
})
export default class InsMenuLayout extends Vue {
  showMemNav: boolean = false;
  private key: string = '';

  private typeList: any[] = [{
    value: 0,
    label: 'Product'
  }, {
    value: 1,
    label: 'CMS'
  }];
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  private searchType: number = 0;

  handleOpen (key, keyPath) {
    console.log(key, keyPath);
  }
  handleClose (key, keyPath) {
    console.log(key, keyPath);
  }

  closeSlideMenu () {
    this.$store.dispatch('isShowMenu', false);
  }

  search () {
     this.searchPro();
  }

  searchPro () {
    this.$store.dispatch('setSearchKey', this.key);
    this.$store.dispatch('isShowMenu', false);
    if (this.key !== '') {
      this.$router.push({
        path: '/product/search',
        name: 'productSearch',
        params: {
          key: this.key
        }
      });
    } else {
        this.$store.dispatch('isShowMenu', false);
      this.$router.push({
        path: '/product/search/-'
      });
    }
  }

  searchCms () {
    this.$store.dispatch('isShowMenu', false);
    this.$router.push({
      path: '/cms/search',
      name: 'cmsSearch',
      params: {
        key: this.key
      }
    });
  }

  get user () {
    return this.$store.state.user;
  }

  get isLogin () {
    return this.$store.state.isLogin;
  }
}
</script>

<style lang="less">
.HeadMeun {
    #menu {
        .el-submenu__icon-arrow {
            display: none;
        }

        .el-submenu__title {
            padding-top: 0.5rem;
            padding-bottom: 0.5rem;
            height: auto!important;
            line-height: unset;
            background: url('/images/mobile/ptx_26.png') no-repeat center center;
            background-color:transparent!important;
            background-size: cover;
            .name{
                font-size: 1.6rem!important;
                color:#b19162;
            }
        }
        .el-menu {
            width: 90%;
            margin: 0 auto;
            background-color: transparent;
            border: 0;
            margin-bottom: 1rem;
            margin-top: 1rem;
            .el-submenu__icon-arrow {
                display: block;
                font-size: 1.6rem;
                color: #b19162;
                right: 30px;
            }

            > li {
                height: auto;
                line-height: unset;
                text-align: center;
                margin-bottom: 1rem;
                >a {
                    color:#b19162;
                    display:block;
                    width: 100%;
                    padding-top: 1rem;
                    padding-bottom: 1rem;
                    margin: 0 auto;
                    font-weight: 500;
                    background: url('/images/mobile/ptx_26.png') no-repeat center center;
                    background-size: cover;
                    background-color:transparent;
                    b{
                        color:#FFF;
                        display: block;
                        width: 100%;
                        font-weight: 500;
                        &:nth-child(1){
                            color:#b19162;
                            font-weight: 500;
                            font-size: 1.6rem;
                        }
                        &:nth-child(2){
                            color:#b19162;
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
      box-shadow: 0px 2px 5px #ccc;
      >li{
        border-bottom: 1px solid #eee;
        margin-bottom: 0px!important;
        > a{
          background: transparent!important;
        }
        .router-link-exact-active {
            background: url('/images/mobile/ptx_27.png') no-repeat center center!important;;
            background-size: cover!important;
        }
      }
    }
    #menu .is-opened > .el-submenu__title{
        background-color: transparent!important;
        .name{
            color:#b19162!important;
        }
    }
    #menu .is-opened > .el-submenu__title .el-submenu__icon-arrow{
        color:#b19162!important;
    }
}
.sidebar-container {
    background-color: #fff !important;
}
</style>

<style scoped lang="less">
.search-box {
    width: 90%;
    height: 3.5rem;
    margin: 0 auto;
    margin-top: 2rem;
    position: relative;
    overflow: hidden;
    margin-bottom: 2rem;
    border: 1px solid #9f1e3c;
    border-radius: 5px;
  /deep/ .el-select {
    width: 30%;
    position: absolute;
    left: 0;
    top: 0;

    .el-input__inner {
        height: 4rem;
        border: 0;
        border-right: 1px solid #DCDFE6;
        border-radius: 0;
        padding: 0 2rem 0 0.5rem;
    }

    .el-input__icon {
      line-height: 4rem;
      font-size: 1rem;
    }
  }

  .search-input {
    width: 100%;
    height: 100%;
    > input {
        width: 100%;
        height: 100%;
        border: 0;
        padding: 0 10% 0 5%;
        box-sizing: border-box;
        font-size: 1.2rem;
        outline: none;
    }

    .searchBtn{
        width: 3.5rem;
        height: 3.5rem;
        display: inline-block;
        cursor: pointer;
        position: absolute;
        right: 0;
        top: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        img{
            width: 60%;
            margin: 0 auto;
            display: block;
        }
    }
  }
}
.header_logo {
    height: 7rem;
    position: relative;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 1rem 0 1.5rem;
    border-bottom: 4px solid #acbd30;
    background-color: #fff;

    .el-icon-close {
        color: #777777;
        font-size: 2.8rem;
    }

    a {
        width: 12rem;
    }

    .slide-menu {
        cursor: pointer;
    }
}
.topLang {
  width: 90%;
  margin: 0 auto;
  display: flex;
  flex-wrap: wrap;
  margin-top: 2rem;
  justify-content: space-between;
  align-items: center;
  .left {
    width: 48%;
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start;
    align-items: center;
  }
  .right {
    width: 48%;
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-end;
    align-items: center;
  }
}
/deep/ .langSwitch {
    font-size: 1.5rem;
    color: #999999;
    text-align: center;
    p {
        font-size: 1.5rem;
        display: inline-block;
        color: #999999;
        margin: 0;
        &.active {
            color: #9f1e3c;
        }
        &:nth-child(1) {
          padding-left: 0px!important;
        }
    }
}
</style>
