<template>
  <v-container>
    <div id="share-top" />
    <v-row>
      <v-col>
        <br>
        <span>当前共有 {{ total }} 条分享，当前页：{{ page }} </span>
      </v-col>
      <v-col>
        <v-switch
          v-model="auto"
          style="float: right"
          label="自动更新"
          @change="autoUpdate"
        />
      </v-col>
    </v-row>

    <v-divider />

    <ShareListCom :share-list="shareList" @delete="deleteShare" />

    <div class="text-center">
      <v-pagination
        v-model="page"
        :length="length"
        :total-visible="10"
        circle
        @input="pageChange"
      />
      <!-- <v-combobox style="float: right" :items="sizeItem" /> -->
    </div>

  </v-container>
</template>

<script>
import ShareListCom from '@/components/share-list.vue'
export default {
  name: 'ShareList',
  components: { ShareListCom },
  data() {
    return {
      shareList: [],
      page: 1,
      length: 0,
      size: 15,
      total: 0,
      showDelete: false,
      deleteData: {
        data: ''
      },
      auto: false,
      snackbar: false,
      message: '删除成功',
      key: '',
      sizeItem: [10, 15, 20, 30, 40, 50, 100],
      interval: () => { return null }
    }
  },
  created() {
    const page = parseInt(this.$route.query.page)
    if (!isNaN(page)) {
      if (page <= 0) {
        this.page = 1
      } else {
        this.page = page
      }
    }
    this.getShareList()
  },
  methods: {
    autoUpdate() {
      if (this.auto) {
        this.interval = window.setInterval(() => {
          this.getShareList()
        }, 10000)
      } else {
        window.clearInterval(this.interval)
      }
    },
    deleteShare(status) {
      if (status) {
        this.getShareList()
      }
    },
    getShareList() {
      // url = `/api/share/list?page=${this.page}&size=${this.size}&key=${encodeURIComponent(this.key)}`
      const url = `/share/list?page=${this.page}&size=${this.size}`
      this.httpGet(url, (json) => {
        this.shareList = json.page.content
        this.total = json.page.totalElements
        this.length = json.page.totalPages
      })
    },
    pageChange(page) {
      this.page = page
      this.$router.push({
        path: this.$router.path,
        query: { page: page }
      })

      this.getShareList()
      document.querySelector('#share-top').scrollIntoView()
    }
  }
}
</script>
