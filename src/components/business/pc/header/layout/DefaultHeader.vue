<template>
<div class="header-layout"  v-cloak>
  <div class="headerBg">
      <div class="headerTop">
          <div class="Inner">
            <div class="leftside">
                <a href="/"><img src="/images/mobile/ptx_04.png"></a>
            </div>
            <div class="rightside">
                <p class="Langcode">
                  <InsLangSwitch class="lang"></InsLangSwitch>
                  <CodeSelect v-if="!isPtx" />
                </p>
                <div class="Search">
                  <div class="search-box">
                      <div class="search-input">
                        <input type="text" v-model="key" @keyup.enter="search" />
                        <span class="searchBtn" @click="search"></span>
                      </div>
                    </div>
                  <div class="cartTop" v-if="isPtx" >
                    <router-link to="/account/GetEnquiry">
                          <i class="handle-icon ptxicon"></i>
                    </router-link>
                </div>
                <Shopcart class="memberLogin" v-if="!isPtx"></Shopcart>
                <InsLogin class="memberLogin"></InsLogin>
                </div>
                <p class="MeunMain">
                      <Menu />
                </p>
            </div>
          </div>
      </div>
  </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({
  components: {
    Menu: () =>
      import('@/components/business/pc/header/InsMenu.vue'),
    Shopcart: () =>
      import('@/components/business/pc/header/InsShoppingCart.vue'),
    InsLogin: () =>
      import('@/components/business/pc/header/InsLogin.vue'),
    InsLangSwitch: () =>
      import('@/components/business/pc/header/InsLangSwitch.vue'),
    CodeSelect: () =>
      import('@/components/business/pc/header/InsCodeSelect.vue')
  }
})
export default class DefaultHeader extends Vue {
  @Prop() private showInFixed!: boolean;

  private key: string = '';

  private typeList: any[] = [{
    value: 0,
    label: 'Product'
  }, {
    value: 1,
    label: 'CMS'
  }];

  private searchType: number = 0;
  get isPtx () {
      if (localStorage.getItem('isPtx') === '0') {
        return false;
      } else {
        return true;
      }
  }
  getMenu () {
    this.$Api.promotion
      .getMenu()
      .then(result => {
        this.$store.dispatch('setHeaderMenus', result.ReturnValue.HeaderMenus);
        this.$store.dispatch('setFooterMenus', result.ReturnValue.FooterMenus);
      })
      .catch(error => {
        console.log(error);
      });
  }

  search () {
    switch (this.searchType) {
      case 0:
        this.searchPro();
        break;
      case 1:
        this.searchCms();
        break;
    }
  }

  searchPro () {
    this.$store.dispatch('setSearchKey', this.key);
    if (this.key !== '') {
      this.$router.push({
        path: '/product/search',
        name: 'productSearch',
        params: {
          key: this.key
        }
      });
    } else {
      this.$router.push({
        path: '/product/search/-'
      });
    }
  }

  searchCms () {
    this.$router.push({
      path: '/cms/search',
      name: 'cmsSearch',
      params: {
        key: this.key
      }
    });
  }

  get currentlang () {
    return this.$i18n.locale;
  }
  private changLange (lang) {
    this.$Api.member
      .setUILanguage(lang)
      .then(result => {
        this.$i18n.locale = lang;
        localStorage.setItem('locale', lang);
        this.Reload();
      })
      .catch(error => {
        console.log(error);
      });
  }

  created () {
    this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
  }

  mounted () {
    this.getMenu();
  }
}
</script>

<style scoped lang="less">
.showMenuYes{
  height:151px;
  transition:all 1s;
}
#header{
  z-index: 9999;
  top:0px;
  width: 100%;
  display: flex;
}
.headerBg{
   width: 100%;
   background:url('/images/pc/pcptx_05.jpg') no-repeat center center;
   background-size: cover;
   display: inline-block;
   border-bottom-left-radius: 10px;
   border-bottom-right-radius: 10px;
   position: absolute;
   z-index: 999;
  .headerTop{
      width: 1200px;
      margin: 0 auto;
      .Inner {
        width: 100%;
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        .leftside {
          width: 30%;
          display: flex;
          align-items: center;
        }
        .rightside {
          width: 68%;
          display: flex;
          flex-wrap: wrap;
          justify-content: flex-end;
          .Langcode {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            justify-content: flex-end;
            margin-top: 7px;
          }
          .Search {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            justify-content: flex-end;
            margin-top: 7px;
          }
          .MeunMain {
            width: 100%;
            display: flex;
            flex-wrap: wrap;
            justify-content: flex-end;
            margin-top: 7px;
            padding-bottom: 7px;
          }
          .memberLogin,.cartTop {
            display: flex;
            align-items: center;
            position: relative;
          }
          .ptxicon {
            background: url('/images/mobile/ptx_10.png') no-repeat center center;
            display: inline-block;
            width: 30px;
            height: 30px;
            background-size: contain;
        }
        }
      }
  }

}
// new css
.header-layout {
 /deep/ .header_menu {
   width: 80%;
   > ul {
     > li {
      float: left;
      display: flex;
      align-items: center;
      position: relative;
      > a {
        width: 100%;
        font-size: 18px;
        color: #ffffff;
        display: block;
        text-align: center;
        font-weight: 500;
        text-transform: uppercase;
        padding: 10px 5px;
      }

      &:hover{
        > a  {
          background: #701027;
          color: #fff;
          border-radius: 20px;
        }
      }

      ul {
        box-shadow: 0 0 5px #ccc;

        li {
          border:0;
          border-bottom: 1px solid #eee;
          &:last-child {
              border:0!important;
          }
          > a {
            font-size: 16px;
            color: #333333;
            display: block;
            text-align: center;
            font-weight: 500;
            text-transform: uppercase;
            padding: 10px 5px;
          }

          &:hover{
              background: #fff url('/images/mobile/meunbg.png') no-repeat center center;
              background-size: contain;
              >a {
                color:#c3a985;
              }
          }
        }
      }
     }
   }
 }
}

.search-box {
  float: left;
  display: flex;
  align-items: center;

  /deep/ .el-select {
    width: 100px;

    .el-input__inner {
      height: 34px;
      line-height: 34px;
      border-radius: 0;
    }

    .el-input__icon {
      line-height: 34px;
    }
  }

  .search-input {
    background: #fff;
    width: 340px;
    display: flex;
    float: left;
    align-items: center;
    margin-right: 20px;
    border-radius: 2px;
    > input {
      width: 305px;
      height: 35px;
      float: left;
      border:none;
      background: transparent;
      line-height: 35px;
      text-indent: 10px;
      outline: 0;
    }

    .searchBtn{
      width: 25px;
      height: 25px;
      display: inline-block;
      background: url('/images/mobile/ptx_25.png') no-repeat center center;
      background-size: 100%;
      cursor: pointer;
    }
  }
}
</style>
