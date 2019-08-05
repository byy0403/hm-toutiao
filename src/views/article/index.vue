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
          <el-select v-model="reqParams.channel_id" placeholder="请选择">
            <el-option
              v-for="item in channelOptions"
              :key="item.value"
              :label="item.label"
              :value="item.value"
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
          ></el-date-picker>
        </el-form-item>
      </el-form>
    </el-card>
    <el-card class="box-card">
      <div slot="header">
        根据筛选条件共查询到
        <b>{{total}}</b>条结果：
      </div>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="date" label="封面" width="180"></el-table-column>
        <el-table-column prop="name" label="标题" width="180"></el-table-column>
        <el-table-column prop="address" label="状态"></el-table-column>
        <el-table-column prop="name" label="发布时间" width="180"></el-table-column>
        <el-table-column prop="address" label="操作"></el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    return {
      reqParams: {
        status: null,
        channel_id: null
      },

      channelOptions: [{ value: 1, label: 'ios' }],
      dateArr: [],
      total: 0
    }
  }
}
</script>

<style scoped lang="less">
.box-card {
  margin-bottom: 20px;
}
</style>
