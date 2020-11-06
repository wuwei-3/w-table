### Table 使用规则

​	目的：统一项目Table样式，快速迭代开发，可根据业务做适当扩展，有意义的UI组件，使用中如有问题，请联系wuwei3@iflytek.com，查看更多二次封装组件：https://git.iflytek.com/HY_bp/biims-ui.git，欢迎大家指正，提供宝贵意见。

- ##### 快速上手

  1. 该组件依赖Element ui 二次封装，使用前必须安装element ui。
  2. 组件内置Scss预处理器，推荐优先使用预处理。

- ##### Attributes

  | 参数       | 说明                     | 类型    | 可选值 |  默认值   |
  | ---------- | ------------------------ | ------- | :----: | :-------: |
  | data       | 列表现实的数据           | Array   |   -    |    []     |
  | column     | 列表头内容               | Array   |   -    |     -     |
  | indexT     | 是否启用 “序号” 列       | Boolean |   -    |   true    |
  | height     | 表格内容高度             | String  |   -    |  “100%”   |
  | color      | 列表头文字颜色           | String  |   -    |  “#666”   |
  | background | 列表头背景颜色           | String  |   -    | “#fafafa” |
  | selection  | 是否启动 “复选框按钮” 列 | Boolean |   -    |   false   |
  | getIndex   | 自动选中行坐标           | Number  |   -    |    -1     |

  

- ##### Table Methods

  | 方法名 | 说明                     | 参数      |
  | ------ | ------------------------ | --------- |
  | row    | 表格单选，选中当前行信息 | row       |
  | select | 表格多选，选中数组信息   | selection |

  

- ##### 页面使用

  ```
  <!--
   * @Description: Table组件Demo
   * @Version: 2.0
   * @Autor: wuwei3
   * @Date: 2020-09-24 14:24:01
   * @LastEditors: Please set LastEditors
   * @LastEditTime: 2020-09-24 19:19:02
  -->
  <template>
    <div class="table">
      <Table :column="column" :data="tableList">
        <!-- 如果有多个插槽直接列表下复制使用 -->
        <template v-slot:detail>
          <el-link type="primary" @click="lockDetail(row)"
            >查看详情</el-link
          >
        </template>
      </Table>
    </div>
  </template>
  
  <script>
  import Table from '@/components/table.vue';
  export default {
    name: 'tableDemo',
    components: {
      Table,
    },
    data() {
      return {
        column: [
          { label: '姓名', prop: 'name' },
          { label: '信息来源', prop: 'message', method: this.message },
          {
            label: '性别',
            prop: 'gender',
            method: this.gender,
          },
          { label: '操作', prop: 'detail', slot: 'detail' },
        ],
        tableList: [
          { name: 'wuwei', gender: 'M' },
          { name: 'liangzhang', gender: 'M' },
        ],
      };
    },
    methods: {
    	/**
       * @description: 信息来源格式化
       * @param {*}
       * @return {*}
       */
      message(row) {
        let msg = row.message;
  
        if (msg === 1) {
          return '<span class="n-green"></span>自主申请';
        }
        return '<span class="n-yellow"></span>专员录入';
      },
      /**
       * @description: 性别格式化
       * @param {row} 当前行信息
       * @param {gender} 字段信息
       * @return {genderName} 男 or 女
       */
      gender(row) {
        return row[‘gender’] === 'M' ? '男' : '女';
      },
       /**
       * @description: 信息审核
       * @param {*}
       * @return {*}
       */
      lockDetail(row) {
        console.log('row',row)
      }
    },
  };
  </script>
  <style lang="scss" scoped>
  .table {
    height: 100%;
    width: 100%;
  }
  </style>
  
  ```

- ##### 模板使用

  ```
  <template>
    <!-- table 页面 -->
    <div class="table-web" :style="{ height: height }">
      <el-table
        :data="data"
        style="width: 100%;"
        :height="height"
        :highlight-current-row="true"
        :row-class-name="tableRowClassName"
        @row-click="rowClick"
        :header-cell-style="{
          background: background,
          color: color,
        }"
        @selection-change="selectionChange"
      >
        <!-- 多选框 -->
        <el-table-column type="selection" width="55" v-if="selection">
        </el-table-column>
        <!-- 序号 -->
  
        <el-table-column
          type="index"
          width="80px"
          label="序号"
          v-if="indexT"
          align="center"
        >
        </el-table-column>
        <!-- 业务逻辑代码 -->
        <el-table-column
          :prop="item.prop"
          :label="item.label"
          :width="item.width"
          v-for="(item, index) in column"
          :key="index"
          align="center"
        >
          <template slot-scope="scope">
            <div>
              <!-- 普通formart格式化 -->
              <div v-if="item.method">
                <span v-html="formart(item, scope.row, item.prop)"></span>
              </div>
              <!-- 操作按钮通过插槽控制 -->
              <div v-else-if="item.slot">
                <slot :name="item.slot" v-bind:row="scope.row"></slot>
              </div>
              <!-- 正常数据展示 -->
              <div v-else>
                <span>{{ scope.row[item.prop] || '-' }}</span>
              </div>
            </div>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </template>
  
  <script>
  export default {
    name: 'Table',
    props: {
      /* 选中行坐标，默认不选中 */
      getIndex: {
        type: Number,
        default: () => {
          return -1;
        },
      },
      /* 多选框展示 */
      selection: {
        type: Boolean,
        default: () => {
          return false;
        },
      },
      /* head背景色 */
      background: {
        type: String,
        default: () => {
          return '#fafafa';
        },
      },
      /* head字体颜色 */
      color: {
        type: String,
        default: () => {
          return '#666';
        },
      },
      /* content表格高度 */
      height: {
        type: String,
        default: () => {
          return '100%';
        },
      },
      /* 序号列表是否展示，默认展示 */
      indexT: {
        type: Boolean,
        default: () => {
          return true;
        },
      },
      /* 列表展示结果 */
      data: {
        type: Array,
        default: () => {
          return [];
        },
      },
      /* head内容 */
      column: {
        type: Array,
        default: () => {
          return [
            { label: '测试1', prop: 'name' },
            { label: '测试2', prop: 'size' },
            { label: '测试3', prop: 'size' },
            { label: '测试4', prop: 'size' },
          ];
        },
      },
    },
    data() {
      return {};
    },
    methods: {
      /**
       * @description:
       * @param {type}
       * @return {type}
       */
      formart(item, row, prop) {
        return item.method(row, prop);
      },
      /**
       * @description: 自动选中行样式更改
       * @param {type}
       * @return:
       */
      tableRowClassName({ row, rowIndex }) {
        row.index = rowIndex;
        if (row.index === 0 && this.getIndex !== -1) {
          return 'current-row';
        }
      },
      /**
       * @description: 当行被点击
       * @param {type}
       * @return:
       */
      rowClick(val) {
        this.$emit('row', val);
      },
  
      /**
       * @description: 多选
       * @param {type}
       * @return:
       */
      selectionChange(val) {
        this.$emit('select', val);
      },
    },
  };
  </script>
  <style lang="scss">
  ::-webkit-scrollbar {
    width: 0;
  }
  .table-web {
    width: 100%;
    height: 500px;
    position: relative;
    .el-table {
      position: absolute;
      height: 500px;
      td {
        padding: 0;
        height: 48px;
        line-height: 48px;
        .cell {
          @include fcc;
        }
      }
    }
  }
  </style>
  
  ```