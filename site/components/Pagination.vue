<template>
  <nav v-if="page.total > 0" class="pagination" role="navigation" aria-label="pagination">
    <a v-if="previousPageUrl" class="pagination-previous" :href="previousPageUrl">上一页</a>
    <a v-else class="pagination-previous" disabled>上一页</a>

    <a v-if="nextPageUrl" class="pagination-previous" :href="nextPageUrl">下一页</a>
    <a v-else class="pagination-previous" disabled>下一页</a>

    <ul class="pagination-list">
      <li v-for="p in pageList" :key="p">
        <a class="pagination-link" :class="{'is-current': p == page.page}" :href="getPageUrl(p)">{{ p }}</a>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: {
    page: { // 页码对象
      type: Object,
      default: function () {
        return { page: 1, total: 0, limit: 20 }
      },
      required: true
    },
    urlPrefix: { // 分页url前缀
      type: String,
      default: '/',
      required: true
    }
  },
  computed: {
    pageList: function () {
      const start = this.page.page - 2
      const end = this.page.page + 2
      const totalPage = this.getTotalPage()
      if (start <= 0) {
        const pages = []
        for (let i = 1; i <= 5 && i <= totalPage; i++) {
          pages.push(i)
        }
        return pages
      } else if (end > totalPage) {
        const pages = []
        let i = totalPage - 5 <= 0 ? 1 : totalPage - 5
        for (; i > 0 && i <= totalPage; i++) {
          pages.push(i)
        }
        return pages
      } else {
        return [
          this.page.page - 2,
          this.page.page - 1,
          this.page.page,
          this.page.page + 1,
          this.page.page + 2
        ]
      }
    },
    previousPageUrl: function () {
      return this.getPreviousPageUrl()
    },
    nextPageUrl: function () {
      return this.getNextPageUrl()
    }
  },
  methods: {
    getNextPageUrl() {
      const nextPage = this.page.page + 1
      if (nextPage > this.getTotalPage()) {
        return ''
      }
      return this.getPageUrl(nextPage)
    },
    getPreviousPageUrl() {
      const previousPage = this.page.page - 1
      if (previousPage <= 0) {
        return ''
      }
      return this.getPageUrl(previousPage)
    },
    getPageUrl(page) {
      return this.urlPrefix + page
    },
    getTotalPage() {
      return this.page.total % this.page.limit > 0
        ? parseInt(this.page.total / this.page.limit) + 1 : this.page.total / this.page.limit
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
