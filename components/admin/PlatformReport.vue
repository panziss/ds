<template>
  <div class="admin-report">
    <el-form :inline="true" class="table-tool-bar">
    	<el-form-item label="操作平台：">
        <el-select v-model="status">
          <el-option v-for="(label,key) in statusList" :label="label" :value="key == '0' ? '' : key" :key="key" />
        </el-select>
      </el-form-item>
      <el-form-item label="操作状态：">
        <el-select v-model="status">
          <el-option v-for="(label,key) in statusList" :label="label" :value="key == '0' ? '' : key" :key="key" />
        </el-select>
      </el-form-item>
      <el-form-item label="时间：">
        <el-date-picker v-model="date" type="daterange" start-placeholder="开始日期" end-placeholder="结束日期" :range-separator="rangeSeparator" @change="handlePicker" ref="datepicker" :picker-options="pickerOptions" @focus="handleFocus" align="right">
        </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" icon="el-icon-search" @click="get">搜索</el-button>
        <el-button @click="reset">重置</el-button>
      </el-form-item>
    </el-form>
    <data-tables-server :data="tableData" :total="total" @query-change="get" v-loading="loading.on" :element-loading-text="loading.text" :pagination-def="paginationDef">
      <el-table-column prop="" label="所属平台" />
      <el-table-column prop="" label="订单编号" />
      <el-table-column prop="" label="账变类型" />
      <el-table-column prop="" label="账变前余额" />
      <el-table-column prop="" label="账变金额" />
      <el-table-column prop="" label="账变后余额" />
      <el-table-column prop="status" label="账变状态">
        <template slot-scope="{row:{status}}">
          <span :class="style(status)">{{statusLabel(status)}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="" label="备注" />
      <el-table-column prop="updated_at" label="申请时间" />

    </data-tables-server>
  </div>
</template>

<script>
import datepicker from '~/util/mixins/datepicker'
import dateTables, { style } from '~/util/mixins/data-tables'


export default {
  name: 'withdraw-report',
  props: ['type', 'serverData'],
  mixins: [datepicker, dateTables],
  data(){
    return {
      listKey:'user_bank_withdraw'
    }
  },
  created() {
    this.fetch(this.listKey)
    this.listen()
  },
  methods: {
    get(loadProps) {
      const { loadType, page } = this.getQueryParams(loadProps)
      if (this.checkPageData(loadProps, page)) return
      if (this.checkDateFormat() === true) {
        const { status, dateParam, resetting } = this
        !resetting && this.spin()
        this.$axiosPlus(
          'user-bank-withdraw/get',
          {
            ...dateParam(),
            status,
            page,
            page_size: loadType ? loadProps.pageSize : this.pageSize
          },
          data => this.pageDone(data, page)
        )
      }
    },
    style(status) {
      return style(status < 3 ? status - 1 : -1)
    }
  }
}
</script>
