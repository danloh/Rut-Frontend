<template>
  <div class="item-page" v-if="itemTitle">
    <div class="item-main">
      <item-sum :item="item" :out="true" :key="item.id"></item-sum> <!--key to re-render-->
      <div>
        <b>More Details</b>
        <span style="float:right">
          <router-link :to="'/update/item/' + itemslug"><small>Edit..</small></router-link>
        </span>
      </div>
      <div class="item-detail">
        <div v-html="showDetail || '...'"></div>
        <el-button type="text" size="mini" @click="showShort=!showShort">
          {{showShort ? '...More' : '..Less'}}
        </el-button>
      </div>
    </div>
    <div class="include">
      <rut-list :per="'item'" :id="itemid"></rut-list>
    </div>
    <div class="item-side">
    </div>
  </div>
</template>

<script>
import ItemSum from '../components/Item/ItemSum.vue'
import RutList from '../components/Rut/RutList.vue'
import marked from '../util/marked'
import { showLess } from '../util/filters'

export default {
  name: 'item-view',
  title () {
    return this.itemTitle
  },
  components: { ItemSum, RutList },
  data () {
    return {
      itemid: '',
      itemslug: '',
      itemTitle: '',
      itemDetail: '',
      showShort: true, // show less detail
    }
  },
  computed: {
    item () {
      return this.$store.state.item.items[this.$route.params.slug]
    },
    showDetail () {
      let content = marked(this.itemDetail)
      let least = 255
      let less = content.length > least && this.showShort
      if (less) {
        return showLess(content, least)
      } else {
        this.showShort = false
        return content
      }
    }
  },
  methods: {
    loadItem() {
      let itemslug = this.itemslug = this.$route.params.slug
      this.$store.dispatch('getItem', itemslug).then(resp => {
        this.itemid = resp.id
        this.itemDetail = resp.detail
        this.itemTitle = resp.title
        this.itemid = resp.id
      })
    },
  },
  created () {
    this.loadItem()
  }
}
</script>

<style scoped>
.item-page {
  padding: 10px 255px 10px 0px;
  position: relative;
}
.item-main {
  margin-top: 5px;
}
.item-detail {
  background-color: #fcfcfa;
  padding: 5px;
}
.item-side {
  position: absolute;
  top: 10px;
  right: 0;
  width: 245px;
}
</style>
