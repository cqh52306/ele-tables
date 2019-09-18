<!--
 * @Descripttion:
 * @version:
 * @Author: caoqinghua
 * @Date: 2019-08-30 14:39:36
 * @LastEditors: Please set LastEditors
 * @LastEditTime: 2019-09-18 16:06:50
 -->
<template>
  <div class="ele-table-box">
    <el-table v-loading="loading" :height="height" :row-class-name="rowClassFn" :data="tableData.list" element-loading-spinner="el-icon-loading" border style="width:100%" class="ele-table" @selection-change="handleSelectionChange" @sort-change="toSort">
      <el-table-column v-if="config.needSelection" type="selection" width="55" />
      <el-table-column v-for="(col,index) in config.tableRows" :fixed="col.fixed ? true : false" :prop="col.prop" :sortable="col.sortable ? col.sortable : false " :label="col.label" :width="col.width ? col.width : 'auto' " :key="index">
        <template slot-scope="scope">
          <span v-if=" !col.operArr && !col.toType" :title="scope.row[col.prop]">
            <!-- 格式化数据 -->
            <template v-if="col.type == 'todetail'">
              <a type="text" class="oper-link" @click="col.openDialog(scope)">{{ scope.row[col.prop] }}</a>
            </template>
            <!-- 格式化数据 -->
            <template v-if="col.type == 'select'">
              {{ getLabelByVal(scope.row[col.prop],col.options) }}
            </template>
            <!-- 日期格式化 yy-mm-dd -->
            <template v-if="col.type == 'yy-mm-dd'">
              {{ formatTime(scope.row[col.prop], col.type) }}
            </template>
            <!-- 日期格式化 时分秒 yy-mm-dd -->
            <template v-if="col.type == 'yy-mm-dd-hh-mm-ss'">
              {{ formatTime(scope.row[col.prop], col.type) }}
            </template>
            <!-- 回调 -->
            <template v-if="col.type == 'feedback'">
              {{ col.cb(scope.row[col.prop]) }}
            </template>
            <!-- 回调 row -->
            <template v-if="col.type == 'feedbackrow'">
              {{ col.cb(scope.row) }}
            </template>
            <!-- 普通 -->
            <template v-if="col.type == undefined">
              {{ scope.row[col.prop] }}
            </template>
            <!-- 下载 -->
            <template v-if="col.type == 'download'">
              <a :style="{cursor:'pointer'}" :href="scope.row.url">{{ scope.row[col.prop] }}</a>
            </template>
          </span>
          <!-- btn按钮组 -->
          <span v-else-if="col.operArr != undefined">
            <a v-for="(oper,operIndex) in col.operArr" v-if="(typeof oper.permission === 'function'?oper.permission(scope.row):oper.permission)" :key="operIndex" type="text" class="oper-link" @click="oper.openDialog(scope.row,oper.name)">{{ typeof oper.name === 'function' ? oper.name(scope.row) : oper.name }}</a>
          </span>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <div v-if="config.needPagination && localPagination.total" class="pageCtrl pageCtrl-box">
      <div class="pageCtrl-left">
        <slot name="user" />
      </div>
      <div v-if="!config.specialPagination" class="pageCtrl-right">
        <el-pagination :page-sizes="localPagination.pageSizes" :page-size="localPagination.pageSize" :layout="localPagination.layout" :total="localPagination.total" :current-page="localPagination.pageIndex" @size-change="handleSizeChange" @current-change="handleCurrentChange" />
      </div>
      <!-- 特殊分页 -->
      <div v-else class="special-pagination-list">
        <div class="pag-sum">共{{ localPagination.realRowCount }}条</div>
        <el-pagination :page-sizes="localPagination.pageSizes" :page-size="localPagination.pageSize" :layout="localPagination.layout" :total="localPagination.total" :current-page="localPagination.pageIndex" @size-change="handleSizeChange" @current-change="handleCurrentChange" />
      </div>
    </div>
  </div>
</template>
<script>
// 通过了value取到label
function getLabelByVal (curr, options) {
  if (!options) {
    return curr
  }
  const temp = options.filter(item => item.curr == curr)
  return temp && temp[0] ? temp[0].label : curr
}

function formatNumber (n) {
  n = n.toString()
  return n[1] ? n : '0' + n
}

// 时间格式化
function formatTime (date, type) {
  const year = date.getFullYear()
  const month = date.getMonth() + 1
  const day = date.getDate()
  const hour = date.getHours()
  const minute = date.getMinutes()
  const second = date.getSeconds()
  if ('yy-mm-dd-hh-mm-ss' == type) {
    return [year, month, day].map(formatNumber).join('-') + ' ' + [hour, minute, second].map(formatNumber).join(':')
  } else {
    return [year, month, day].map(formatNumber).join('-')
  }
}

export default {
  name: 'ele-tables',
  data () {
    return {
      selections: [],
      getLabelByVal,
      formatTime
    }
  },
  props: {
    height: {
      type: Number,
      default: 300
    },
    loading: {
      type: Boolean,
      default: false
    },
    pagination: {
      type: Object,
      default () {
        return {
          pageSize: 20, // 每页显示条目个数
          pageSizes: [20, 50], // 每页显示个数选择器的选项设置
          layout: "total, sizes, prev, pager, next" //, jumper组件布局，子组件名用逗号分隔
        }
      }
    },
    selectionFn: {
      type: Array,
      default () {
        return [];
      }
    },
    routeChange: {
      type: Function,
      default: function () { }
    },
    tableData: {
      type: Object,
      default () {
        return {
          list: []
        }
      }
    },
    config: {
      type: Object,
      default () {
        return {
          tableRows: []
        }
      }
    }
  },
  computed: {
    localPagination: function () {
      const { pageIndex, pageSize } = this.$route.query
      return {
        pageIndex: pageIndex ? parseInt(pageIndex) : 1,
        pageSizes: this.pagination.pageSizes,
        pageSize: pageSize ? parseInt(pageSize) : this.pagination.pageSize,
        layout: this.pagination.layout,
        total: this.tableData.rowCount,
        realRowCount: this.tableData.realRowCount ? this.tableData.realRowCount : 0
      }
    }
  },
  watch: {
    // 利用watch方法检测路由变化
    '$route': function (to, from) {
      if (to.path == from.path) {
        this.handleRouteChange({ type: 'change' })
      } else {
        this.tableData.list = []
        this.handleRouteChange({ type: 'jump' })
      }
    }
  },
  methods: {
    handleRouteChange (params) {
      this.routeChange(params)
    },
    // 每页显示条数改变
    handleSizeChange (pageSize) {
      this.$router.push({
        path: this.$route.path,
        query: {
          ...this.$route.query,
          pageSize
        }
      })
    },
    // 页码改变
    handleCurrentChange (pageIndex) {
      this.$router.push({
        path: this.$route.path,
        query: {
          ...this.$route.query,
          pageIndex
        }
      })
    },
    // 操作复选框
    handleSelectionChange (selections) {
      this.selections = selections
      if (this.selectionFn) {
        this.selectionFn(selections)
      }
    },
    rowClassFn ({ row, rowIndex }) {
      if (this.config.rowClassFn) {
        return this.config.rowClassFn({ row, rowIndex })
      }
    },
    toSort ({ prop, order }) {
      var params = {}
      params['orderBy'] = prop
      if (order == 'descending') {
        params['isDesc'] = 1
      } else {
        params['isDesc'] = ''
      }
      this.$router.push({
        path: this.$route.path,
        query: {
          ...this.$route.query,
          ...params
        }
      })
    }
  }
}

</script>
<style lang='scss' scoped>
.oper-link {
  cursor: pointer;
  & + & {
    margin-left: 20px;
  }
}
.pageCtrl-box {
  display: flex;
  // padding-top: 20px;
  .pageCtrl-left {
    // margin-top: 20px;
    height: 32px;
    flex: 1;
  }
}
</style>
