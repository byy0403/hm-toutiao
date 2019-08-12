<template>
  <div class="image-container">
    <!-- 图片按钮 -->
    <div class="img-btn" @click="opendialog">
      <img :src="value || defaultImage" alt />
    </div>
    <!-- 对话框 -->
    <el-dialog :visible.sync="dialogVisible" width="700">
      <el-tabs v-model="activeName" type="card">
        <el-tab-pane label="素材库" name="image">
          <el-radio-group v-model="reqParams.collect" size="mini" @change="collectChange">
            <el-radio-button :label="false">全部</el-radio-button>
            <el-radio-button :label="true">收藏</el-radio-button>
          </el-radio-group>
          <div class="img-list">
            <div
              class="img-item"
              @click="selectedImage(item.url)"
              :class="{selected:selectedImageUrl===item.url}"
              v-for="item in images"
              :key="item.id"
            >
              <img :src="item.url" alt />
            </div>
          </div>
          <el-pagination
            background
            layout="prev, pager, next"
            :total="total"
            :page-size="reqParams.per_page"
            :current-page="reqParams.page"
            @current-change="changePager"
          ></el-pagination>
        </el-tab-pane>
        <el-tab-pane label="上传图片" name="upload">
          <el-upload
            class="avatar-uploader"
            action="http://ttapi.research.itcast.cn/mp/v1_0/user/images"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :headers="uploadHeaders"
            name="image"
          >
            <img v-if="uploadImageUrl" :src="uploadImageUrl" class="avatar" />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
        </el-tab-pane>
      </el-tabs>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="confirmImage()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import defaultImage from '../assets/images/default.png'
import store from '@/store'
export default {
  name: 'my-image',
  props: ['value'],
  data () {
    return {
      reqParams: {
        collect: false,
        page: 1,
        per_page: 8
      },
      images: [],
      dialogVisible: false,
      activeName: 'image',
      total: 0,
      defaultImage,
      // 记录当前选中的图片地址
      selectedImageUrl: null,
      // 上传头部
      uploadHeaders: {
        Authorization: `Bearer ${store.getUser().token}`
      },
      // 记录上传图片的地址
      uploadImageUrl: null
    }
  },
  mounted () {
    this.getImages()
  },
  methods: {
    // 开启封面
    opendialog () {
      this.dialogVisible = true
      // 清空之前选中或者上传的图片地址
      this.selectedImageUrl = null
      this.uploadImageUrl = null
      // 获取素材列表数据
      this.getImages()
    },
    // 获取全部素材
    async getImages () {
      const {
        data: { data }
      } = await this.$http.get('user/images', {
        params: this.reqParams
      })
      this.images = data.results
      this.total = data.total_count
    },
    // 切换收藏
    collectChange () {
      this.reqParams.page = 1
      this.getImages()
    },
    // 分页
    changePager (newPage) {
      this.reqParams.page = newPage
      this.getImages()
    },
    // 选中图片
    selectedImage (url) {
      this.selectedImageUrl = url
    },
    // 成功上传图片
    handleAvatarSuccess (res) {
      this.uploadImageUrl = res.data.url
    },
    // 确认图片
    confirmImage () {
      if (this.activeName === 'image') {
        this.$emit('input', this.selectedImageUrl)
      } else {
        this.$emit('input', this.uploadImageUrl)
      }
      this.dialogVisible = false
    }
  }
}
</script>
<style scoped lang="less">
</style>
