<template>
  <div>
    <h3>商品列表</h3>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区域 -->
    <el-card>
      <!-- 搜索与添加区域 -->
      <el-row :gutter="20">
        <el-col :span="7">
          <el-input
            v-model="queryInfo.query"
            placeholder="请输入内容"
            clearable
            @clear="getUserList"
          >
            <el-button
              slot="append"
              icon="el-icon-search"
              @click="getUserList"
            ></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="addGoods">添加商品</el-button>
        </el-col>
        <!-- 商品列表 -->
        <el-table :data="goodsList" stripe border>
          <el-table-column type="index"></el-table-column>
          <el-table-column prop="goods_name" label="商品名称">
          </el-table-column>
          <el-table-column prop="goods_price" label="商品价格(元)">
          </el-table-column>
          <el-table-column prop="goods_weight" label="商品重量">
          </el-table-column>
          <el-table-column prop="add_time" label="创建时间" width="140px">
            <template slot-scope="scope">
              {{ scope.row.add_time | dateFormat }}
            </template>
          </el-table-column>
          <el-table-column fixed="right" label="操作" width="125px">
            <template slot-scope="scope">
              <el-button
                type="primary"
                icon="el-icon-edit"
                size="small"
                @click="showEditDialog(scope.row.goods_id)"
              ></el-button>
              <el-button
                type="danger"
                icon="el-icon-delete"
                size="small"
                @click="removeGoodsById(scope.row.goods_id)"
              ></el-button>
            </template>
          </el-table-column>
        </el-table>
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
      ></el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 查询参数
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 10
      },
      goodsList: [], // 商品数据
      total: 0 // 总数据条数
    }
  },
  // 获取商品数据
  methods: {
    async getGoodsList() {
      const { data: res } = await this.$http.get('goods', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品列表数据失败')
      }
      console.log(res.data)
      this.goodsList = res.data.goods
      this.total = res.data.total
    },
    // 当每页数量发生变化时触发
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getGoodsList()
    },
    // 当页码发生变化时触发
    handleCurrentChange(newPage) {
      this.queryInfo.pagenum = newPage
      this.getGoodsList()
    },
    // 删除商品
    async removeGoodsById(goodsId) {
      const confirmResult = await this.$confirm('确定删除该商品？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      if (confirmResult !== 'confirm') {
        return
      }
      const { data: res } = await this.$http.delete(`goods/${goodsId}`)
      console.log(res)
      if (res.meta.status !== 200) {
        return this.$message.error('删除商品失败')
      }
      this.$message.success('删除商品成功')
      this.getGoodsList()
    },
    // 添加商品
    addGoods() {
      this.$router.push('/goods/add')
    }
  },
  created() {
    this.getGoodsList()
  }
}
</script>
