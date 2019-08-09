<template>
  <el-card>
    <div slot="header">
      <my-bread>素材管理</my-bread>
    </div>
    <div class="btn_box">
      <el-radio-group v-model="reqParams.collect" @change="changeCollect" size="small">
        <el-radio-button :label="false">全部</el-radio-button>
        <el-radio-button :label="true">收藏</el-radio-button>
      </el-radio-group>
      <el-button type="success" @click="openDialog()" size="small" style="float:right">添加素材</el-button>
    </div>
    <div class="img_list">
      <div class="img_item" v-for="item in images" :key="item.id">
        <img :src="item.url" alt />
        <div class="foot" v-show="!reqParams.collect">
          <span
            class="el-icon-star-off"
            @click="toggleCollect(item)"
            :class="{selected:item.is_collected}"
          ></span>
          <span class="el-icon-delete" @click="del(item.id)"></span>
        </div>
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

    <!-- 弹出框 -->
    <el-dialog title="添加素材" :visible.sync="dialogVisible" width="300px">
      <el-form>
        <el-upload
          class="avatar-uploader"
          action="http://ttapi.research.itcast.cn/mp/v1_0/user/images"
          :show-file-list="false"
          :on-success="handleAvatarSuccess"
          name="image"
          :headers="uploadHeaders"
        >
          <img v-if="imageUrl" :src="imageUrl" class="avatar" />
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
import store from '@/store'
export default {
  data () {
    return {
      reqParams: {
        collect: false,
        page: 1,
        per_page: 10
      },
      images: [],
      total: 0,
      dialogVisible: false,
      imageUrl: null,
      // 上传组件的头部信息
      uploadHeaders: {
        Authorization: `Bearer ${store.getUser().token}`
      }
    }
  },
  methods: {
    async getImages () {
      const {
        data: { data }
      } = await this.$http.get('user/images', { params: this.reqParams })
      this.images = data.results
      this.total = data.total_count
    },
    // 切换收藏,数据双向绑定
    changeCollect () {
      this.reqParams.page = 1
      this.getImages()
    },
    // 分页
    changePager (newpage) {
      this.reqParams.page = newpage
      this.getImages()
    },
    // 添加素材
    openDialog () {
      this.dialogVisible = true
    },
    // 素材上传
    handleAvatarSuccess (res) {
      // 获取图片地址
      this.imageUrl = res.data.url
      // 提示上传成功
      this.$message.success('上传成功')
      // 过两秒关闭
      window.setTimeout(() => {
        this.dialogVisible = false
        this.reqParams.page = 1
        this.getImages()
      })
    },
    // 添加收藏 ，取消收藏
    async toggleCollect (item) {
      const {
        data: { data }
      } = await this.$http.put(`user/images/${item.id}`, {
        collect: !item.is_collected
      })
      this.$message.success(data.collect ? '收藏成功' : '取消收藏成功')
      // 改变当前图片状态
      item.is_collected = data.collect
    },
    // 删除
    del (id) {
      this.$confirm('此操作将永久删除该文件, 请问是否继续?', '温馨提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          await this.$http.delete(`user/images/${id}`)
          this.$message.success('删除成功')
          this.getImages()
        })
        .catch(() => {})
    }
  },
  created () {
    this.getImages()
  }
}
</script>

 <style lang="less" scoped>
.img_list {
  margin-top: 20px;
  .img_item {
    width: 160px;
    height: 160px;
    border: 1px dashed #ddd;
    position: relative;
    display: inline-block;
    margin-right: 34px;
    margin-bottom: 20px;
    img {
      width: 100%;
      height: 100%;
      display: block;
    }
    .foot {
      position: absolute;
      width: 100%;
      height: 30px;
      line-height: 30px;
      left: 0;
      bottom: 0;
      background: rgba(0, 0, 0, 0.5);
      color: #fff;
      text-align: center;
      span {
        margin: 0 20px;
        &.selected {
          color: red;
        }
      }
    }
  }
}
</style>
