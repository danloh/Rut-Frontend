<template>
  <div>
    <b v-if="action !== '0'">{{ action | titleCase }}: {{ totalCount }}</b>
    <div class="rut-list">
      <rut-sum v-for="rut in ruts" :key="rut.id" :rut="rut"></rut-sum>
    </div>
    <div v-if="hasMore">
      <el-button size="mini" @click="loadMoreRut" :disabled="!hasMore">
        Show More
      </el-button>
    </div>
  </div>
</template>

<script>
import RutSum from '../Rut/RutSum.vue'
import { fetchRuts } from '../../api'

export default {
  name: 'rut-list',
  props: {
    per: String,   // should be user,item,tag
    action: {type: String, default: '0'},
    id: String,
  },
  components: { RutSum },
  data () {
    return {
      perid: '',
      totalCount: 0,
      ruts: [],
      paging: 1,
    }
  },
  computed: {
    hasMore () {
      return this.ruts.length < this.totalCount
    }
  },
  methods: {
    loadRuts () {
      // chinldren route, cannot pass the id
      let pid = this.perid = this.id ? this.id : this.$route.params.id
      fetchRuts(this.per, pid, this.paging, this.action).then(resp => {
        this.ruts = resp.data.ruts
        this.$store.commit('SET_RUTS', this.ruts)  // as cache
        this.totalCount = resp.data.count
      })
    },
    loadMoreRut () {
      fetchRuts(this.per, this.perid, this.paging+1, this.action).then(resp => {
        let more = resp.data.ruts
        this.$store.commit('SET_RUTS', more)  // as cache
        this.ruts.push(...more)
        this.paging += 1
      })
    }
  },
  watch: {
    '$route.params.id': 'loadRuts'
  },
  created () {
    this.loadRuts()
  }
}
</script>

<style scoped>
.rut-list {
  width: 100%;
  margin-top: 5px;
}
</style>
