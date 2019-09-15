<!--
 * @Descripttion:
 * @version:
 * @Author: caoqinghua
 * @Date: 2019-08-26 20:16:30
 * @LastEditors: caoqinghua
 * @LastEditTime: 2019-09-12 13:50:48
 -->

# ele-tables

## 一、使用方式

- 1、安装

  ```shell
  npm install ele-tables
  或者
  yarn add ele-tables
  ```

- 2、在项目中的`main.js`文件中引入

  ```js
  import EleTables from "ele-tables"
  import "ele-tables/lib/ele-tables.css"
  Vue.use(EleTables)
  ```

* 3、组件中使用

  ```html
  <template>
    <div id="app">
      <ele-tables
        :pagination="pagination"
        :route-change="handleRouteChange"
        :config="tablesConfig"
        :table-data="interfaceData"
      ></ele-tables>
    </div>
  </template>

  <script>
    export default {
      name: "app",
      data() {
        return {
          pagination: {
            pageSize: 20, // 每页显示条目个数
            pageSizes: [20, 50], // 每页显示个数选择器的选项设置
            layout: "total, sizes, prev, pager, next" //, jumper组件布局，子组件名用逗号分隔
          },
          tablesConfig: {
            needPagination: true, // 是否需要分页
            tableRows: [
              { prop: "productName", label: "产品名称" },
              { prop: "name", label: "姓名" },
              { prop: "mobile", label: "手机号", width: 130 },
              { prop: "certNo", label: "身份证号" },
              {
                prop: "startTime",
                label: "开始时间",
                width: 180,
                type: "yy-mm-dd-hh-mm-ss" // 内置两类日期时间格式化yy-mm-dd-hh-mm-ss 和 yy-mm-dd
              },
              {
                prop: "",
                label: "操作",
                operArr: [
                  {
                    name: "详情",
                    permission: true,
                    openDialog: row => {
                      this.openDialog(row, col)
                    }
                  },
                  { name: "编辑", permission: true }
                ]
              }
            ]
          },
          interfaceData: {
            list: [
              {
                productName: "微信",
                name: "张三",
                mobile: "185****3322",
                certNo: "3101011995030*****",
                startTime: new Date()
              }
            ],
            rowCount: 1
          }
        }
      },
      methods: {
        handleRouteChange(params) {
          const { type } = params
          if (type == "change") {
            console.log("参数改变，请求后端接口")
          }
        },
        openDialog() {}
      }
    }
  </script>
  ```

## 二、主要的`API`

|      参数      |    类型    | 说明                                      | 默认值 |
| :------------: | :--------: | ----------------------------------------- | ------ |
|  `pagination`  |  `Object`  | 配置分页                                  | 空     |
| `route-change` | `Function` | 路由改变执行函数                          |        |
|    `config`    |  `Object`  | 表格配置项，具体如上                      | `{}`   |
|  `table-data`  |  `Object`  | 后端接口数据，list:列表,rowCount:分页条数 |        |
