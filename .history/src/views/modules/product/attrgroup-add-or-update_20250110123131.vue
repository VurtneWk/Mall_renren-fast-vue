<template>
  <el-dialog :title="!dataForm.attrGroupId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible"
    @closed="dialogClose">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
      label-width="120px">
      <el-form-item label="组名" prop="attrGroupName">
        <el-input v-model="dataForm.attrGroupName" placeholder="组名"></el-input>
      </el-form-item>
      <el-form-item label="排序" prop="sort">
        <el-input v-model="dataForm.sort" placeholder="排序"></el-input>
      </el-form-item>
      <el-form-item label="描述" prop="descript">
        <el-input v-model="dataForm.descript" placeholder="描述"></el-input>
      </el-form-item>
      <el-form-item label="组图标" prop="icon">
        <el-input v-model="dataForm.icon" placeholder="组图标"></el-input>
      </el-form-item>
      <el-form-item label="所属分类" prop="catelogId">
        <!--      <el-input v-model="dataForm.catelogId" placeholder="所属分类id"></el-input>-->
        <el-cascader v-model="dataForm.catelogIds" :options="categorys" :props="props" filterable></el-cascader>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      categorys: [],
      props: {
        label: 'name',
        value: 'catId',
        children: 'children'
      },
      visible: false,
      dataForm: {
        attrGroupId: 0,
        attrGroupName: '',
        sort: '',
        descript: '',
        icon: '',
        catelogIds: [],
        catelogId: 0
      },
      dataRule: {
        attrGroupName: [
          { required: true, message: '组名不能为空', trigger: 'blur' }
        ],
        sort: [
          { required: true, message: '排序不能为空', trigger: 'blur' }
        ],
        descript: [
          { required: true, message: '描述不能为空', trigger: 'blur' }
        ],
        icon: [
          { required: true, message: '组图标不能为空', trigger: 'blur' }
        ],
        catelogId: [
          { required: true, message: '所属分类id不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    getMenus() {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({ data }) => {
        console.log('成功获取到菜单数据。。', data)
        this.categorys = data.data
        //尝试前端去掉空children 后端给去掉了
        // this.categorys = this.getTreeData(data.data)
      })
    },
    getTreeData(data) {
      for (var i = 0; i < data.length; i++) {
        if (data[i].children.length < 1) {
          // children若为空数组，则将children设为undefined
          data[i].children = undefined;
        } else {
          // children若不为空数组，则继续 递归调用 本方法
          this.getTreeData(data[i].children);
        }
      }
      return data;
    },
    dialogClose() {
      this.dataForm.catelogIds = []
    },
    init(id) {
      this.dataForm.attrGroupId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.attrGroupId) {
          this.$http({
            url: this.$http.adornUrl(`/product/attrgroup/info/${this.dataForm.attrGroupId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.attrGroupName = data.attrGroup.attrGroupName
              this.dataForm.sort = data.attrGroup.sort
              this.dataForm.descript = data.attrGroup.descript
              this.dataForm.icon = data.attrGroup.icon
              this.dataForm.catelogId = data.attrGroup.catelogId
              this.dataForm.catelogIds = data.attrGroup.catelogPath
              //查出catelogid的完整路劲
            }
          })
        }
      })
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/product/attrgroup/${!this.dataForm.attrGroupId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'attrGroupId': this.dataForm.attrGroupId || undefined,
              'attrGroupName': this.dataForm.attrGroupName,
              'sort': this.dataForm.sort,
              'descript': this.dataForm.descript,
              'icon': this.dataForm.icon,
              'catelogId': this.dataForm.catelogIds[this.dataForm.catelogIds.length - 1]
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.visible = false
                  this.$emit('refreshDataList')
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    }
  },
  created() {
    this.getMenus()
  }
}
</script>
