<template>
  <el-tree :data="menus" :props="defaultProps" :expand-on-click-node="false" show-checkbox node-key="catId"
    :default-expanded-keys="expandedKey">
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button v-if="node.level <= 2" type="text" size="mini" @click="() => append(data)">
          Append
        </el-button>
        <el-button v-if="node.childNodes.length === 0" type="text" size="mini" @click="() => remove(node, data)">
          Delete
        </el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
/* eslint-disable */
export default {
  data() {
    return {
      expandedKey: [],
      menus: [],
      defaultProps: {
        children: 'childrenList',
        label: 'name'
      }
    }
  },
  methods: {
    getMenus() {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        console.log('成功获取到菜单数据...', data.data)
        this.menus = data.data
      })
    },
    append(data) {
      console.log('append', data)
    },

    remove(node, data) {
      let ids = [data.catId]
      this.$confirm(`是否删除【${data.name}】菜单`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/product/category/delete'),
          method: 'post',
          data: this.$http.adornData(ids, false)
        }).then(({ data }) => {
          this.$message({
            type: 'success',
            message: '菜单删除成功!'
          });
          this.getMenus()
          //设置需要默认展开的菜单
          this.expandedKey = [node.parent.data.catId];
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    }
  },
  created() {
    this.getMenus()
  }
}
</script>

<style scoped lang="scss"></style>
