<template>
  <div>
    <h3>添加商品</h3>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>添加商品</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区域 -->
    <el-card>
      <el-alert
        title="添加商品信息"
        type="info"
        show-icon
        :closable="false"
      ></el-alert>
      <!-- 步骤组件 -->
      <el-steps
        :active="activeIndex - 0"
        finish-status="success"
        align-center
        :space="200"
      >
        <el-step title="基本信息"></el-step>
        <el-step title="商品参数"></el-step>
        <el-step title="商品属性"></el-step>
        <el-step title="商品图片"></el-step>
        <el-step title="商品内容"></el-step>
        <el-step title="完成"></el-step>
      </el-steps>
      <!-- tab栏区域 -->
      <el-form
        :model="addForm"
        :rules="addFormRules"
        ref="addFormRef"
        label-width="150px"
      >
        <el-tabs
          v-model="activeIndex"
          :tab-position="'left'"
          :before-leave="beforeTabLeave"
          @tab-click="tabClicked"
        >
          <el-tab-pane label="基本信息" name="0">
            <el-form-item label="商品名称" prop="goods_name">
              <el-input v-model="addForm.goods_name"></el-input>
            </el-form-item>
            <el-form-item label="商品价格" prop="goods_price">
              <el-input v-model="addForm.goods_price" type="number"></el-input>
            </el-form-item>
            <el-form-item label="商品重量" prop="goods_weight">
              <el-input v-model="addForm.goods_weight" type="number"></el-input>
            </el-form-item>
            <el-form-item label="商品数量" prop="goods_number">
              <el-input v-model="addForm.goods_number" type="number"></el-input>
            </el-form-item>
            <el-form-item label="商品分类" prop="goods_cat">
              <el-cascader
                expandTrigger="hover"
                v-model="addForm.goods_cat"
                :options="cateList"
                :props="cateProps"
                @change="handleChange"
                clearable
              ></el-cascader>
            </el-form-item>
          </el-tab-pane>
          <el-tab-pane label="商品参数" name="1">
            <el-form-item
              v-for="item in manyTableData"
              :key="item.attr_id"
              :label="item.attr_name"
            >
              <el-checkbox-group v-model="item.attr_vals">
                <el-checkbox
                  v-for="(item2, index2) in item.attr_vals"
                  :key="index2"
                  :label="item2"
                  border
                ></el-checkbox>
              </el-checkbox-group>
            </el-form-item>
          </el-tab-pane>
          <el-tab-pane label="商品属性" name="2">
            <el-form-item
              v-for="item in onlyTableData"
              :key="item.attr_id"
              :label="item.attr_name"
            >
              <el-input v-model="item.attr_vals"></el-input>
            </el-form-item>
          </el-tab-pane>
          <el-tab-pane label="商品图片" name="3">
            <!-- 商品图片上传
            action:指定图片上传api接口
            :on-preview ： 当点击图片时会触发该事件进行预览操作,处理图片预览
            :on-remove : 当用户点击图片右上角的X号时触发执行
            :on-success：当用户点击上传图片并成功上传时触发
            list-type ：设置预览图片的方式
            :headers ：设置上传图片的请求头 -->
            <el-upload
              :action="uploadUrl"
              :on-preview="handlePreview"
              :on-remove="handleRemove"
              :on-success="handleSuccess"
              :file-list="fileList"
              list-type="picture"
              :headers="headerObj"
            >
              <el-button size="small" type="primary">点击上传</el-button>
              <div slot="tip" class="el-upload__tip">
                只能上传jpg/png文件，且不超过500kb
              </div>
            </el-upload>
          </el-tab-pane>
          <el-tab-pane label="商品内容" name="4">
            <!-- 富文本编辑器组件 -->
            <quill-editor v-model="addForm.goods_introduce"></quill-editor>
            <el-button type="primary" class="btnAdd" @click="add"
              >添加商品</el-button
            >
          </el-tab-pane>
        </el-tabs>
      </el-form>
    </el-card>
    <!-- 预览图片对话框 -->
    <el-dialog title="图片预览" :visible.sync="previewVisible" width="50%">
      <img :src="previewPath" class="previewImg" />
    </el-dialog>
  </div>
</template>

<script>
import _ from 'lodash' // 导入深度拷贝工具

export default {
  data() {
    return {
      activeIndex: '0', // 激活页面的索引id
      addForm: {
        goods_name: '',
        goods_price: 0,
        goods_weight: 0,
        goods_number: 0,
        goods_cat: [],
        pics: [], // 上传图片的数组
        goods_introduce: '', // 商品详情
        attrs: [] // 动态参数和静态属性
      },
      manyTableData: [], // 动态参数列表
      onlyTableData: [], // 静态属性列表
      // 验证规则
      addFormRules: {
        goods_name: [
          { required: true, message: '请输入商品名称', trigger: 'blur' }
        ],
        goods_price: [
          { required: true, message: '请输入商品价格', trigger: 'blur' }
        ],
        goods_weight: [
          { required: true, message: '请输入商品重量', trigger: 'blur' }
        ],
        goods_number: [
          { required: true, message: '请输入商品数量', trigger: 'blur' }
        ],
        goods_cat: [
          { required: true, message: '请选择商品分类', trigger: 'blur' }
        ]
      },
      cateList: [], // 分类数据
      cateProps: {
        value: 'cat_id',
        label: 'cat_name',
        children: 'children'
      },
      uploadUrl: 'http://101.132.108.64:8888/api/private/v1/upload', // 上传图片的url地址
      // 图片上传的组件的header请求头对象
      headerObj: { Authorization: window.sessionStorage.getItem('token') },
      previewPath: '', // 预览图片的url地址
      previewVisible: false // 控制预览图片对话框的显示和隐藏
    }
  },
  methods: {
    async getCateList() {
      const { data: res } = await this.$http.get('categories')
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品分类数据失败')
      }
      this.cateList = res.data
    },
    // 级联选择改变
    handleChange() {
      // 必须选择三级分类
      if (this.addForm.goods_cat.length !== 3) {
        this.addForm.goods_cat = []
      }
    },
    // tab标签点击时触发
    async tabClicked() {
      console.log('activeIndex:' + this.activeIndex)
      if (this.activeIndex === '1') {
        // 动态参数
        const { data: res } = await this.$http.get(
          `categories/${this.cateId}/attributes`,
          {
            params: { sel: 'many' }
          }
        )
        if (res.meta.status !== 200) {
          return this.$message.error('获取动态参数失败')
        }
        res.data.forEach(item => {
          item.attr_vals =
            item.attr_vals.length === 0 ? [] : item.attr_vals.split(' ')
        })
        console.log(res)
        this.manyTableData = res.data
      } else if (this.activeIndex === '2') {
        // 静态属性
        const { data: res } = await this.$http.get(
          `categories/${this.cateId}/attributes`,
          {
            params: { sel: 'only' }
          }
        )
        if (res.meta.status !== 200) {
          return this.$message.error('获取静态属性失败')
        }
        this.onlyTableData = res.data
      }
    },
    // tab栏切换之前触发
    beforeTabLeave(activeName, oldActiveName) {
      if (oldActiveName === '0') {
        // 第一个标签页
        if (this.addForm.goods_cat.length !== 3) {
          this.$message.error('请选择商品的分类')
          return false
        }
        // else if (this.addForm.goods_name.trim() === '') {
        //   this.$message.error('请输入商品名称')
        //   return false
        // } else if (this.addForm.goods_price <= 0) {
        //   this.$message.error('请输入商品价格')
        //   return false
        // } else if (this.addForm.goods_weight <= 0) {
        //   this.$message.error('请输入商品重量')
        //   return false
        // } else if (this.addForm.goods_number <= 0) {
        //   this.$message.error('请输入商品数量')
        //   return false
        // }
      }
    },
    // 当用户点击图片进行预览时触发
    handlePreview(file) {
      console.log(file)
      this.previewPath = `http://101.132.108.64:8888/${file.response.data.tmp_path}`
      this.previewVisible = true
    },
    // 当用户删除时执行
    handleRemove(file) {
      const filePath = file.response.data.tmp_path
      const index = this.addForm.pics.findIndex(item => item.pic === filePath)
      this.addForm.pics.splice(index, 1)
      console.log(this.addForm)
    },
    // 当图片上传成功时执行
    handleSuccess(response) {
      // 将服务返回的临时路径保存在addForm表单的pics数组中
      this.addForm.pics.push({ pic: response.data.tmp_path })
      console.log(this.addForm)
    },
    // 添加商品
    add() {
      this.$refs.addFormRef.validate(async valid => {
        if (!valid) {
          return this.$message.error('请填写必要的表单项')
        }
        // 执行添加的业务逻辑
        // lodash cloneDeep(obj) 深度拷贝
        const form = _.cloneDeep(this.addForm)
        form.goods_cat = form.goods_cat.join(',') // 将数组转为字符串 1，2，3
        // 处理动态参数
        this.manyTableData.forEach(item => {
          const newInfo = {
            attr_id: item.attr_id,
            attr_value: item.attr_vals.join(' ')
          }
          this.addForm.attrs.push(newInfo)
        })
        // 处理静态属性
        this.onlyTableData.forEach(item => {
          const newInfo = {
            attr_id: item.attr_id,
            attr_value: item.attr_vals
          }
          this.addForm.attrs.push(newInfo)
        })
        form.attrs = this.addForm.attrs
        console.log(form)

        // 添加商品的请求
        // 商品的名称，必须是唯一的
        const { data: res } = await this.$http.post('goods', form)
        console.log(res)
        if (res.meta.status !== 201) {
          return this.$message.error('添加商品失败')
        }
        this.$message.success('添加商品成功')
        this.$router.push('/goods')
      })
    }
  },
  created() {
    this.getCateList()
  },
  computed: {
    cateId() {
      if (this.addForm.goods_cat.length === 3) {
        return this.addForm.goods_cat[2]
      }
      return null
    }
  }
}
</script>

<style lang="less" scoped>
.el-checkbox {
  margin: 0 15px 0 0 !important;
}
.previewImg {
  width: 100%;
}
.btnAdd {
  margin-top: 15px;
}
</style>
