<script>
export default {
  data () {
    return {
      menus: [],
      defaultProps: {
        children: 'childrenList',
        label: 'name'
      }
    }
  },
  methods: {
    getMenus () {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        console.log('成功获取到菜单数据...', data.data)
        this.menus = data.data
      })
    },
    handleNodeClick (data) {
      console.log(data)
    }
  },
  created () {
    this.getMenus()
  }
}
</script>

<template>
  <el-tree :data="menus" :props="defaultProps" @node-click="handleNodeClick"></el-tree>
</template>

<style scoped lang="scss">

</style>
