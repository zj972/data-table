<template>
  <div class="TableChart">
    <header class="header">
      <div class="header-left">
        <span>{{headlined}}</span>
        <el-popover
          v-if="hasHelp"
          ref="popover"
          placement="right-start"
          :title="helpTitle"
          width="200"
          trigger="click"
          :content="helpContent">
        </el-popover>
        <el-button
          size="small"
          v-if="hasHelp"
          v-popover:popover
          type="text">
          <i class="el-icon-fa-question-circle"></i>
        </el-button>
        <div
          class="select"
          v-for="(option, index) in selectOptions"
          :key="index">
          <span>{{ option.title }}：</span>
          <el-select
            size="mini"
            popper-class="header-popper"
            v-model="option.value"
            @change="tc_selected(index)"
            placeholder="请选择">
            <el-option
              v-for="item in option.data"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-select>
        </div>
      </div>
      <div class="header-right">
        <el-button
          v-if="hasChart"
          type="text"
          size="small"
          @click="dataType = 'chart'"
          icon="fa-line-chart">
        </el-button>
        <el-button
          v-if="hasTable"
          type="text"
          size="small"
          @click="dataType = 'table'"
          icon="fa-table">
        </el-button>
      </div>
    </header>
    <e-charts
      v-if="dataType === 'chart'"
      :height="chart_height"
      :options="tc_chart_options">
    </e-charts>
    <data-table
      v-if="dataType === 'table'"
      :tableData="table_tableData"
      :height="table_height"
      :isSelection="table_isSelection"
      :selectionKey="table_selectionKey"
      :emptyText="table_emptyText"
      :fileName="table_fileName"
      :isPagination="table_isPagination"
      :pageSize="table_pageSize"
      :hasDetail="table_hasDetail"
      :hasEdit="table_hasEdit"
      :hasDelete="table_hasDelete"
      :options="table_options"
      v-on:selection="table_selection"
      v-on:detail="table_detail"
      v-on:edit="table_edit"
      v-on:deleted="table_deleted"
    ></data-table>
  </div>
</template>

<script>
  import dataTable from './DataTable';
  import eCharts from './ECharts';

  export default {
    name: 'TableChart',
    components: {
      dataTable,
      eCharts
    },
    props: {
      /* -----------------public----------------- */
      // （选填）remark:标题
      headlined: {
        type: String,
        default: ''
      },
      hasHelp: {
        type: Boolean,
        default: false
      },
      // （帮助必填） remark:帮助标题
      helpTitle: {
        type: String,
        default: '暂无备注信息:)'
      },
      // （帮助必填）type:Object[] remark:帮助内容
      helpContent: {
        type: String,
        default: ''
      },
      // （选填）remark:表格高度
      hasChart: {
        type: Boolean,
        default: true
      },
      // （选填）remark:表格高度
      hasTable: {
        type: Boolean,
        default: true
      },
      /**
       * （必填）
       * @param title type:String remark:标题
       * @param value type:String remark:索引
       * @param data type:Array remark:数据项
       */
      selectOptions: {
        type: Array,
        default: []
      },
      /* -----------------table----------------- */
      // （必填）type:Object[] remark:表格数据
      table_tableData: Array,
      // （选填）remark:表格高度
      table_height: Number,
      // （选填）remark:是否多选
      table_isSelection: {
        type: Boolean,
        default: false
      },
      // （多选必填）多选的关键字
      table_selectionKey: {
        type: String,
        default: ''
      },
      // （选填）remark:数据为空时显示的文本内容
      table_emptyText: {
        type: String,
        default: '暂无数据'
      },
      // （选填）remark:导出文件名称
      table_fileName: {
        type: String,
        default: 'table'
      },
      // （选填）remark:是否分页
      table_isPagination: {
        type: Boolean,
        default: true
      },
      // （选填）remark:默认显示行数
      table_pageSize: {
        type: Number,
        default: 10
      },
      // （选填）remark:是否需要删除按钮
      table_hasDelete: {
        type: Boolean,
        default: false
      },
      // （选填）remark:是否需要详情按钮
      table_hasDetail: {
        type: Boolean,
        default: false
      },
      // （选填）remark:是否需要编辑按钮
      table_hasEdit: {
        type: Boolean,
        default: false
      },
      /**
       * 必填
       * @param prop type:string remark:对应数据字段
       * @param label type:string remark:表头
       * 选填
       * @param fixed type:Boolean|'left'|'right'(true === left) remark:列固定
       * @param sortable type:Boolean|'custom' remark:排序
       * @param type type: 'string'|'number' remark:字段类型
       */
      table_options: Array,
      /* -----------------chart----------------- */
      // （必填）remark:表格配置文件，详情见echarts
      chart_height: {
        type: String,
        default: ''
      },
      chart_options: {
        type: Object,
        default: {}
      }
    },
    data () {
      return {
        /* -----------------输入----------------- */
        tc_chart_options: {
          title: {
            show: false
          },
          tooltip: {
            trigger: 'axis',
            axisPointer: {
              type: 'cross',
              label: {}
            }
          },
          yAxis: {},
          toolbox: {
            feature: {
              magicType: {
                type: ['line', 'bar']
              },
              restore: {}
            },
            right: 20
          }
        },
        /* -----------------内部----------------- */
        // table or chart
        dataType: 'table'
      };
    },
    computed: {},
    beforeMount () {
      // 初始化数据
      for (var index in this.chart_options) {
        if (this.chart_options.hasOwnProperty(index)) {
          this.tc_chart_options[index] = this.chart_options[index];
        }
      }
    },
    mounted () {
    },
    methods: {
      /* -----------------输出----------------- */
      table_selection (id) {
        console.log(id);
        this.$emit('selection', id);
      },
      table_detail (row) {
        console.log(row);
        this.$emit('detail', row);
      },
      table_edit (row) {
        console.log(row);
        this.$emit('edit', row);
      },
      table_deleted (row) {
        console.log(row);
        this.$emit('deleted', row);
      },
      tc_selected (index) {
        console.log(this.selectOptions[index].label, this.selectOptions[index].value);
        this.$emit('selected', this.selectOptions[index].label, this.selectOptions[index].value);
      }
      /* -----------------内部----------------- */
    },
    destroyed () {}
  };
</script>

<style lang="scss" scoped>
.TableChart {
  width: 100%;
  font-size: 12px;
  line-height: 16px;
  border: 1px solid #ddd;
  box-shadow: 0 2px 2px 0 rgba(0, 0, 0, .2);
  border-radius: 3px;
  background: #fff;
  .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 10px;
    border-bottom: 1px solid #ddd;
    background: #f6f7f9;
    .header-left {
      .el-button {
        margin: 0 10px;
      }
      .select {
        display: inline-block;
        margin: 0 5px;
      }
    }
  }
}
</style>
<<style lang="scss">
.header-popper {
  .el-select-dropdown__item {
    padding: 0 10px;
    height: inherit;
  }
}
</style>

