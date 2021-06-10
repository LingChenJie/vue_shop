<template>
  <div>
    <h3>商品分类</h3>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区域 -->
    <el-card>
      <!-- 搜索与添加区域 -->
      <el-row>
        <el-col>
          <el-button type="primary" @click="showAddCateDialog"
            >添加分类</el-button
          >
          <!-- 分类表格
          :data(设置数据源) :columns(设置表格中列配置信息) :selection-type(是否有复选框)
          :expand-type(是否展开数据) show-index(是否设置索引列) index-text(设置索引列头)
          border(是否添加纵向边框) :show-row-hover(是否鼠标悬停高亮) -->
          <tree-table
            :data="cateList"
            :columns="columns"
            :selection-type="false"
            :expand-type="false"
            show-index
            border
            index-text="#"
            :show-row-hover="true"
          >
            <!-- 是否有效 -->
            <template slot="isok" slot-scope="scope">
              <i
                class="el-icon-success"
                v-if="scope.row.cat_deleted === false"
                style="color: lightgreen;"
              ></i>
              <i class="el-icon-error" v-else style="color: red;"></i>
            </template>
            <!-- 排序 -->
            <template slot="order" slot-scope="scope">
              <el-tag size="small" v-if="scope.row.cat_level === 0"
                >一级</el-tag
              >
              <el-tag
                size="small"
                v-else-if="scope.row.cat_level === 1"
                type="success"
                >二级</el-tag
              >
              <el-tag size="small" v-else type="warning">三级</el-tag>
            </template>
            <!-- 操作 -->
            <template slot="opt">
              <el-button type="primary" icon="el-icon-edit" size="small"
                >编辑</el-button
              >
              <el-button type="danger" icon="el-icon-delete" size="small"
                >删除</el-button
              >
            </template>
          </tree-table>
        </el-col>
      </el-row>
      <!-- 分页区域 -->
      <el-pagination
        background
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[1, 2, 5, 10]"
        :page-size="queryInfo.pagesize"
      >
      </el-pagination>
    </el-card>
    <!-- 添加分类对话框 -->
    <el-dialog
      title="添加分类"
      :visible.sync="addCateDialogVisible"
      width="50%"
      @close="addDialogClose"
    >
      <el-form
        ref="addFormRef"
        :rules="addFormRules"
        :model="addForm"
        label-width="80px"
      >
        <el-form-item label="分类名称" prop="cat_name">
          <el-input v-model="addForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级分类" prop="cat_pid">
          <!-- expandTrigger='hover'(鼠标悬停触发级联) v-model(设置级联菜单绑定数据)
          :options(指定级联菜单数据源)  :props(用来配置数据显示的规则)
          clearable(提供“X”号完成删除文本功能) change-on-select(是否可以选中任意一级的菜单) -->
          <el-cascader
            class="cascader"
            v-model="selectedKeys"
            :options="parentCateList"
            :props="cascaderProps"
            @change="handleParentCateChange"
            expand-trigger="hover"
            change-on-select
            clearable
          ></el-cascader>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addCateDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addCate">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cateList: [], // 商品列表数据
      queryInfo: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },
      total: 0, // 总数据数据条数
      columns: [
        {
          label: '分类名称',
          prop: 'cat_name'
        },
        {
          label: '是否有效',
          // 将当前列定义为模板列
          type: 'template',
          // 表示当前这一列使用的模板名称
          template: 'isok'
        },
        {
          label: '排序',
          type: 'template',
          template: 'order'
        },
        {
          label: '操作',
          type: 'template',
          template: 'opt'
        }
      ],
      // 添加分类表单
      addForm: {
        cat_name: '',
        cat_pid: 0, // 添加分类的父级id，0则表示父级为0.添加一级分类
        cat_level: 0 // 添加分类的等级，0则表示添加一级分类
      },
      parentCateList: [], // 父级商品列表数据
      addCateDialogVisible: false, // 添加分类对话框显示/隐藏
      selectedKeys: [],
      // 配置级联菜单中数据如何展示
      cascaderProps: {
        value: 'cat_id',
        label: 'cat_name',
        children: 'children',
        expandTrigger: 'hover'
      },
      // 添加分类校验规则
      addFormRules: {
        cat_name: [
          { required: true, message: '请输入分类名称', trigger: 'blur' }
        ]
      }
    }
  },
  created() {
    this.getCateList()
  },
  methods: {
    // 获取商品数据
    async getCateList() {
      const { data: res } = await this.$http.get('categories', {
        params: this.queryInfo
      })
      console.log(res)
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品数据失败')
      }
      this.cateList = res.data.result
      this.total = res.data.total
    },
    // 当当前页展示数量发生变化时触发
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getCateList()
    },
    // 当页码发生改变时触发
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage
      this.getCateList()
    },
    // 显示添加分类对话框
    async showAddCateDialog() {
      // 获取父级分类数据列表
      const { data: res } = await this.$http.get('categories', {
        params: { type: 2 }
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品分类列表数据失败')
      }
      this.parentCateList = res.data
      console.log('父级商品列表数据')
      console.log(this.parentCateList)
      this.addCateDialogVisible = true
    },
    // 处理父级选择级联表改变
    handleParentCateChange() {
      console.log(this.selectedKeys)
      // 如果选择了父级分类
      if (this.selectedKeys.length > 0) {
        this.addForm.cat_pid = this.selectedKeys[this.selectedKeys.length - 1]
        this.addForm.cat_level = this.selectedKeys.length
      } else {
        // 没有选择父级分类
        this.addForm.cat_pid = 0
        this.addForm.cat_level = 0
      }
    },
    // 关闭了添加分类的对话框
    addDialogClose() {
      this.$refs.addFormRef.resetFields()
      this.selectedKeys = []
      this.addForm.cat_pid = 0
      this.addForm.cat_level = 0
    },
    // 点击了添加分类确定
    addCate() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) {
          return this.$message.error('请完善数据')
        }
        const { data: res } = await this.$http.post('categories', this.addForm)
        if (res.meta.status !== 201) {
          return this.$message.error('添加分类失败')
        }
        this.$message.success('添加分类成功')
        this.getCateList()
        this.addCateDialogVisible = false
      })
    }
  }
}
</script>

<style lang="less" scope>
.el-cascader {
  width: 100%;
}
.el-cascader-panel {
  height: 200px;
}
</style>
