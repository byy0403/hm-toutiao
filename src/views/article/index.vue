<template>
  <div>
    <el-card class="box-card">
      <div slot="header">
        <my-bread>内容管理</my-bread>
      </div>
      <el-form label-width="80px" size="small">
        <el-form-item label="状态：">
          <el-radio-group v-model="reqParams.status">
            <el-radio :label="null">全部</el-radio>
            <el-radio :label="1">草稿</el-radio>
            <el-radio :label="2">待审核</el-radio>
            <el-radio :label="3">审核通过</el-radio>
            <el-radio :label="4">审核失败</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="频道：">
          <el-select v-model="reqParams.channel_id" clearable placeholder="请选择">
            <el-option
              v-for="item in channelOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="日期：">
          <el-date-picker
            v-model="dateArr"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            @change="changeDate"
            value-format="yyyy-MM-dd"
          ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="search()">筛选</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <el-card class="box-card">
      <div slot="header">
        根据筛选条件共查询到
        <b>{{total}}</b>条结果：
      </div>
      <el-table :data="articles" style="width: 100%">
        <el-table-column prop="date" label="封面">
          <template slot-scope="scope">
            <el-image :src="scope.row.cover.images[0]" fit="cover" style="width:120px;height:80px">
              <div slot="error">
                <img
                  src="../../assets/images/error.gif"
                  fit="cover"
                  style="width:120px;height:80px"
                />
              </div>
            </el-image>
          </template>
        </el-table-column>
        <el-table-column prop="title" label="标题" width="180"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 0" type="info">草稿</el-tag>
            <el-tag v-if="scope.row.status === 1">待审核</el-tag>
            <el-tag v-if="scope.row.status === 2" type="success">审核通过</el-tag>
            <el-tag v-if="scope.row.status === 3" type="warning">审核失败</el-tag>
            <el-tag v-if="scope.row.status === 4" type="danger">删除</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="pubdate" label="发布时间"></el-table-column>
        <el-table-column label="操作" width="120px">
          <template slot-scope="scope">
            <el-button type="primary" plain @click="edit(scope.row.id)" icon="el-icon-edit" circle></el-button>
            <el-button type="danger" plain @click="del(scope.row.id)" icon="el-icon-delete" circle></el-button>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        style="text-align:center;margin-top:20px"
        background
        layout="prev, pager, next"
        :total="total"
        :page-size="reqParams.per_page"
        :current-page="reqParams.page"
        @current-change="changePager"
      ></el-pagination>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    return {
      reqParams: {
        status: null,
        channel_id: null,
        begin_pubdate: null,
        end_pubdate: null,
        page: 1,
        per_page: 20
      },

      channelOptions: [],
      articles: [],
      dateArr: [],
      total: 0
    }
  },
  methods: {
    // 获取下拉选项数据
    async getChannelOptions () {
      const {
        data: { data }
      } = await this.$http.get('channels')
      this.channelOptions = data.channels
    },
    // 获取文章列表数据
    async getArticles () {
      const {
        data: { data }
      } = await this.$http.get('articles', { params: this.reqParams })
      this.articles = data.results
      this.total = data.total_count
    },
    /// / 改变分页事件对应函数
    changePager (newPage) {
      this.reqParams.page = newPage
      this.getArticles()
    },
    // 筛选
    search () {
      this.reqParams.page = 1
      this.getArticles()
    },
    // 日期选择后的事件
    changeDate (dateArr) {
      // 严谨，考虑用户时间清空
      if (dateArr) {
        this.reqParams.begin_pubdate = dateArr[0]
        this.reqParams.end_pubdate = dateArr[1]
      } else {
        this.reqParams.begin_pubdate = null
        this.reqParams.end_pubdate = null
      }
    },
    // 删除文章
    del (id) {
      // 提示确认框
      this.$confirm('此操作将永久删除该文件, 请问是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          await this.$http.delete(`articles/${id}`)
          this.$message.success('删除文章成功')
          this.getArticles()
        })
        .catch(() => {})
    },
    // 编辑文章
    edit (id) {
      this.$router.push('/publish?id=' + id)
    }
  },

  created () {
    // 获取下拉选项数据
    this.getChannelOptions()
    // 获取文章列表数据
    this.getArticles()
  },
  watch: {
    // watch 侦听器的使用场景：当你需要监听某一个属性的变化，去做一些开销较大操作(异步操作),当然单纯的其他操作也行。
    'reqParams.channel_id': function (newVal, oldVal) {
      if (newVal === '') {
        this.reqParams.channel_id = null
      }
    }
  }
}
</script>

<style scoped lang="less">
.box-card {
  margin-bottom: 20px;
}
</style>
