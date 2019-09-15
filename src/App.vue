<!--
 * @Descripttion:
 * @version:
 * @Author: caoqinghua
 * @Date: 2019-08-26 20:16:30
 * @LastEditors: caoqinghua
 * @LastEditTime: 2019-09-12 10:44:08
 -->
<template>
  <div id="app">
    <div>列表</div>
    <ele-tables :pagination="pagination" :route-change="handleRouteChange" :config="tablesConfig" :table-data="interfaceData"></ele-tables>
  </div>
</template>

<script>
import EleTables from '../packages/ele-tables/table';
export default {
  name: 'app',
  components: {
    EleTables
  },
  data () {
    return {
      pagination: {
        pageSize: 20, // 每页显示条目个数
        pageSizes: [20, 50], // 每页显示个数选择器的选项设置
        layout: 'total, sizes, prev, pager, next' //, jumper组件布局，子组件名用逗号分隔
      },
      tablesConfig: {
        needPagination: true,
        tableRows: [
          { prop: 'productName', label: '产品名称' },
          { prop: 'name', label: '姓名' },
          { prop: 'mobile', label: '手机号', width: 130 },
          { prop: 'certNo', label: '身份证号' },
          { prop: 'startTime', label: '开始时间', width: 180, type: 'yy-mm-dd-hh-mm-ss' },
          // {
          //   prop: '',
          //   label: '操作',
          //   operArr: [
          //     { name: '详情', permission: true, openDialog: (row) => { this.openDialog(row, col) } },
          //     { name: '编辑', permission: true }
          //   ]
          // }
        ]
      },
      interfaceData: {
        list: [{
          productName: '微信',
          name: '张三',
          mobile: '185****3322',
          certNo: '3101011995030*****',
          startTime: new Date()
        }],
        rowCount: 1
      }
    }
  },
  methods: {
    handleRouteChange (params) {
      const { type } = params
      if (type == 'change') {
        console.log('参数改变，请求后端接口')
      }
    }
  }
}
</script>

<style lang="scss">
</style>
