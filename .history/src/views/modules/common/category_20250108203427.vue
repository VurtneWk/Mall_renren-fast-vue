<template>
  <el-tree :data="menus" :props="defaultProps" ref="menuTree" @node-click="nodeClick">
  </el-tree>
</template>

<script>
export default {
  data() {
    return {
      menus: [],
      defaultProps: {
        children: 'childrenList',
        label: 'name'
      },
    }
  },
  methods: {
    getMenus() {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        console.log('成功获取到菜单数据。。', data)
        this.menus = data.data
      })
    },
    // 共三个参数，依次为：传递给 data 属性的数组中该节点所对应的对象、节点对应的 Node、节点组件本身。
    nodeClick(data, node, component) {
      console.log('分类组件点击-》', data, node, component)
      this.$emit('category-node-click', data, node, component)
    }
  },
  created() {
    this.getMenus()
  }
}
</script>

<style lang="scss" scoped></style>
