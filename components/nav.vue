<template>
  <nav class="navbar">
    <div class="navbar-brand is-size-4">
      <nuxt-link to="/" class="navbar-item navbar-logo" id="logoGreen">
        <span class="sbercoin-icon" /> Sbercoin.com
      </nuxt-link>
      <button type="button" class="button navbar-burger" @click="showMenu = !showMenu">
        <span></span><span></span><span></span>
      </button>
    </div>
    <div class="navbar-menu" :class="{'is-active': showMenu}">
      <div class="navbar-start is-uppercase">
        <AttributeInjector class="navbar-item" @click.native="showMenu = !showMenu">
          <nuxt-link to="/block">{{ $tc('blockchain.blockInNav') }}</nuxt-link>
          <nuxt-link to="/contract/tokens">{{ $tc('blockchain.token') }}</nuxt-link>
          <!-- <nuxt-link to="/misc/charts" class="navbar-link">{{ $t('misc.misc') }}</nuxt-link> -->
          <nuxt-link to="/misc/charts" class="navbar-item">{{ $t('misc.charts_title') }}</nuxt-link>
          <nuxt-link to="/misc/rich-list" class="navbar-item">{{ $t('misc.rich_list_title') }}</nuxt-link>
          <div class="has-dropdown is-hoverable">
            <nuxt-item to="/misc/charts" class="navbar-link">{{ $t('misc.misc') }}</nuxt-item>
            <div class="navbar-dropdown is-boxed">
              <a href="https://wallet.sbercoin.com/" class="navbar-item">{{ $t('misc.web_wallet') }}
              </a>
              <a href="https://ide.sbercoin.com/" class="navbar-item">Sbercoin IDE
              </a>
            </div>
          </div>
        </AttributeInjector>
      </div>
      <form class="navbar-end" @submit.prevent="search">
        <div class="navbar-item input-item">
          <input type="text" class="input" v-model="searchString" :placeholder="$t('nav.search')">
          <button type="submit" class="button is-sbercoin" :class="{'is-loading': searching}">
            <Icon icon="search" />
          </button>
        </div>
      </form>
    </div>
  </nav>
</template>

<script>
  import {get as sbercoininfoGet} from '@/services/sbercoininfo-api'

  export default {
    data() {
      return {
        showMenu: false,
        searchString: '',
        searching: false
      }
    },
    methods: {
      async search() {
        let searchString = this.searchString.trim()
        if (!searchString || this.searching) {
          return
        }
        this.searching = true
        try {
          let {type, id, address} = await sbercoininfoGet(`/search`, {params: {query: searchString}})
          switch (type) {
          case 'address':
            this.searchString = ''
            this.$router.push(`/address/${searchString}`)
            break
          case 'block':
            this.searchString = ''
            this.$router.push(`/block/${searchString}`)
            break
          case 'contract':
          case 'qrc20':
          case 'qrc721':
            this.searchString = ''
            if (address) {
              this.$router.push(`/contract/${address}`)
            } else {
              this.$router.push(`/contract/${searchString}`)
            }
            break
          case 'transaction':
            this.searchString = ''
            this.$router.push(`/tx/${searchString}`)
            break
          }
        } catch (err) {}
        this.showMenu = false
        this.searching = false
      }
    }
  }
</script>

<style lang="less" scoped>
  .navbar-logo {
    display: inline-block;
    .sbercoin-icon {
      vertical-align: middle;
    }
  }
  .navbar-end {
    flex: auto;
    align-items: center;
    .navbar-item {
      flex: auto;
      position: relative;
      input {
        padding-right: 3em;
      }
      button {
        position: absolute;
        top: 0.5em;
        right: 0;
      }
    }
  }
</style>
