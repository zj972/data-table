<template>
  <div class="DataTable">
    <header class="table-header">
      <div class="search">
        <el-input
          v-model="searchTable"
          placeholder="输入关键字搜索"
          icon="fa-search"
          size="mini">
        </el-input>
      </div>
      <div class="switch-button">
        <el-button
          type="text"
          size="small"
          @click="copyTable()"
          icon="fa-files-o">
          <span>复制</span>
        </el-button>
        <el-button
          type="text"
          size="small"
          @click="downloadCSV()"
          icon="fa-download">
          <span>导出CSV</span>
        </el-button>
      </div>
    </header>
    <el-table
      :data="d_tableData"
      :height="d_height"
      :empty-text="d_emptyText"
      :row-key="d_selectionKey"
      @selection-change="handleSelectionChange"
      @sort-change="sortTable"
      v-loading="tableLoading"
      element-loading-text="拼命加载中"
      border>
      <el-table-column
        v-if="d_isSelection"
        type="selection"
        fixed="left"
        :reserve-selection="true"
        width="46">
      </el-table-column>
      <el-table-column
        v-for="(option, index) in d_options"
        :prop="option.prop"
        :label="option.label"
        :key="index"
        :min-width="option.width"
        :fixed="option.fixed"
        :show-overflow-tooltip="option.showOverflowTooltip"
        :sortable="option.sortable">
      </el-table-column>
      <el-table-column
        v-if="d_hasDelete || d_hasDetail || d_hasEdit"
        fixed="right"
        label="操作"
        width="110">
        <template scope="scope">
          <el-button
            v-if="hasDetail"
            type="text"
            @click="d_detail(d_tableData[scope.$index])"
            icon="fa-bar-chart"
            size="small">
          </el-button>
          <el-button
            v-if="hasEdit"
            type="text"
            @click="d_edit(d_tableData[scope.$index])"
            icon="fa-pencil-square-o"
            size="small">
          </el-button>
          <el-button
            v-if="hasDelete"
            type="text"
            @click="d_deleted(d_tableData[scope.$index])"
            icon="fa-trash-o"
            size="small">
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      v-if="d_isPagination"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[5, 10, 20, 50, 99]"
      :page-size="d_pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="tableLength">
    </el-pagination>
  </div>
</template>

<script>
  import Clipboard from 'clipboard';
  export default {
    name: 'DataTable',
    props: {
      // （必填）type:Object[] remark:表格数据
      tableData: Array,
      // （选填）remark:表格高度
      height: Number,
      // （选填）remark:是否多选
      isSelection: {
        type: Boolean,
        default: false
      },
      // （多选必填）多选的关键字
      selectionKey: {
        type: String,
        default: ''
      },
      // （选填）remark:数据为空时显示的文本内容
      emptyText: {
        type: String,
        default: '暂无数据'
      },
      // （选填）remark:导出文件名称
      fileName: {
        type: String,
        default: 'table'
      },
      // （选填）remark:是否分页
      isPagination: {
        type: Boolean,
        default: true
      },
      // （选填）remark:默认显示行数
      pageSize: {
        type: Number,
        default: 10
      },
      // （选填）remark:是否需要删除按钮
      hasDelete: {
        type: Boolean,
        default: false
      },
      // （选填）remark:是否需要详情按钮
      hasDetail: {
        type: Boolean,
        default: false
      },
      // （选填）remark:是否需要编辑按钮
      hasEdit: {
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
      options: Array
    },
    data () {
      return {
        /* -----------------输入----------------- */
        // d_tableData: [],
        d_height: '',
        d_isSelection: false,
        d_selectionKey: '',
        d_emptyText: '',
        d_fileName: 'table',
        d_options: [],
        d_isPagination: true,
        d_pageSize: 10,
        d_hasDelete: false,
        d_hasDetail: false,
        d_hasEdit: false,

        /* -----------------内部----------------- */
        // 搜索框数据
        searchTable: '',
        // 保存剪切板插件对象
        clipboard: Object,
        // 当前页数
        currentPage: 1,
        // 表格长度
        tableLength: Number,
        // 选中值
        multipleSelection: [],
        // 排序数组
        sortData: [],
        // loading
        tableLoading: true
      };
    },
    computed: {
      // 根据搜索框数据以及分页动态显示表格数据
      d_tableData: {
        get () {
          let data = [];
          if (this.searchTable === '' || !this.sortData.length) {
            data = [...this.sortData];
          } else {
            data = [...this.sortData];
            for (let i = 0; i < data.length;) {
              let key = false;
              for (var item in data[i]) {
                if (data[i].hasOwnProperty(item)) {
                  // 正则匹配
                  key = key || (new RegExp(this.searchTable, 'i').test(data[i][item]));
                }
              }
              if (!key) {
                data.splice(i, 1);
              } else {
                i++;
              }
            }
          }
          this.tableLength = data.length;

          return data.slice(((this.currentPage - 1) * this.d_pageSize), (this.currentPage * this.d_pageSize));
        },
        set (val) {
          return val;
        }
      }
    },
    beforeMount () {
      // 初始化数据
      // this.d_tableData = [...this.tableData];
      this.sortData = [...this.tableData];
      this.d_height = this.height;
      this.d_isSelection = this.isSelection;
      this.d_selectionKey = this.selectionKey;
      this.d_isPagination = this.isPagination;
      this.d_options = this.options;
      this.d_pageSize = this.pageSize;
      this.d_hasDelete = this.hasDelete;
      this.d_hasDetail = this.hasDetail;
      this.d_hasEdit = this.hasEdit;
    },
    mounted () {
      this.setWidth();
      const obj = this;
      this.$nextTick(() => {
        obj.tableLoading = false;
      });
    },
    methods: {
      /* -----------------输出----------------- */
      /**
       * @function selection remark:选择行触发事件
       * @param id type:String remark:选中表格数据(selectionKey)
       */
      handleSelectionChange (val) {
        let id = [];
        for (let i = 0; i < val.length; i++) {
          id.push(val[i][this.d_selectionKey]);
        }
        this.multipleSelection = [...id];
        this.$emit('selection', id);
      },
      /**
       * @function detail remark:详情按钮点击事件
       * @param row type:Object remark:事件行数据
       */
      d_detail (row) {
        this.$emit('detail', row);
      },
      /**
       * @function edit remark:编辑按钮点击事件
       * @param row type:Object remark:事件行数据
       */
      d_edit (row) {
        this.$emit('edit', row);
      },
      /**
       * @function delete remark:删除按钮点击事件
       * @param row type:Object remark:事件行数据
       */
      d_deleted (row) {
        this.$emit('deleted', row);
      },

      /* -----------------内部----------------- */
      // 复制表格内容到剪切板
      copyTable () {
        const csv = this.getCSV();
        this.clipboard = new Clipboard('.switch-button', {
          text () {
            return csv;
          }
        });
      },
      // 导出CSV
      downloadCSV () {
        let blob = new Blob(['\ufeff', this.getCSV()], {
          type: 'text/csv;charset=unicode;'
        });
        let link = document.createElement('a');
        link.style.display = 'none';
        document.body.appendChild(link);
        link.setAttribute('href', URL.createObjectURL(blob));
        link.setAttribute('download', `${this.fileName}.csv`);
        document.body.appendChild(link);
        link.click();
      },
      // 获取表格文本数据
      getCSV () {
        const columns = this.d_options;
        const data = this.d_tableData;
        const csvSeparator = ',';
        let csv = '';

        // headers
        for (let i = 0; i < columns.length; i++) {
          csv += columns[i].label;
          if (i < (columns.length - 1)) {
            csv += csvSeparator;
          }
        }

        // body
        data.forEach((record) => {
          csv += '\n';
          for (let i = 0; i < columns.length; i++) {
            csv += record[columns[i].prop];
            if (i < (columns.length - 1)) {
              csv += csvSeparator;
            }
          }
        });

        return csv;
      },
      // 每页显示数改变
      handleSizeChange (val) {
        this.d_pageSize = val;
        this.currentPage = 1;
      },
      // 页数改变
      handleCurrentChange (val) {
        this.currentPage = val;
      },
      // 排序
      sortTable (sort) {
        console.log(sort);
        const order = sort.order;
        const prop = sort.prop;
        let type;
        this.d_options.forEach((item) => {
          if (item.prop === prop) {
            type = item.type;
          }
        });
        if (order === null) {
          this.sortData = this.tableData;
        } else {
          if (type === 'number') {
            this.sortData.sort((a, b) => {
              if (order === 'ascending') {
                return a[prop] - b[prop];
              } else if (order === 'descending') {
                return b[prop] - a[prop];
              }
            });
          } else {
            this.sortData.sort((a, b) => {
              if (order === 'ascending') {
                return a[prop] > b[prop] ? 1 : -1;
              } else if (order === 'descending') {
                return a[prop] < b[prop] ? 1 : -1;
              }
            });
          }
        }
      },
      // 计算宽度
      setWidth () {
        const data = this.tableData.slice(0, 20);
        const width = {};
        for (let key in data[0]) {
          if (data[0].hasOwnProperty(key)) {
            width[key] = 0;
          }
        }
        data.forEach((item) => {
          for (let key in item) {
            if (item.hasOwnProperty(key) && item[key].toString().length > width[key]) {
              width[key] = item[key].toString().length;
            }
          }
        });
        console.log(width);
        this.d_options.forEach((item) => {
          if (item.type === 'number') {
            item.width = width[item.prop] * 8 + 40;
          } else {
            item.width = width[item.prop] * 12 + 40;
          }
        });
      }
    },
    destroyed () {
      // 消除剪切板插件对象
      this.clipboard.destroy;
    }
  };
</script>

<style lang="scss" scoped>
.DataTable {
  font-size: 12px;
  line-height: 16px;
  background: #fff;
  .table-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 10px;
  }
  .el-table {
    font-size: 12px;
  }
  .el-pagination {
    text-align: center;
    background: #f6f7f9;
  }
}
</style>
<style lang="scss">
.DataTable {
  .el-table__header th,
  .el-table__body td{
    height: 30px;
  }
}
</style>

