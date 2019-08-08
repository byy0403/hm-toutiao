<template>
  <el-select :value="value" @change="fn" clearable placeholder="请选择">
    <el-option v-for="item in channelOptions" :key="item.id" :label="item.name" :value="item.id"></el-option>
  </el-select>
</template>

<script>
export default {
  name: 'my-channel',
  props: ['value'],
  data () {
    return {
      channelOptions: []
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
    fn (val) {
      if (val === '') {
        val = null
      }
      this.$emit('input', val)
    }
  },
  created () {
    this.getChannelOptions()
  }
}
</script>

<style>
</style>
