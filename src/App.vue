<template>
  <div id="app">
    <table-chart
      :headlined="'图表'"
      :hasHelp="true"
      :helpTitle="app_helpTitle"
      :helpContent="app_helpContent"
      :selectOptions="app_selectOptions"

      :table_tableData="app_tableData"
      :table_isSelection="true"
      :table_selectionKey="'id'"
      :table_isPagination="true"
      :table_pageSize="10"
      :table_hasDetail="true"
      :table_hasEdit="true"
      :table_hasDelete="true"
      :table_options="app_table_options"

      :chart_options="app_chart_options"
      :chart_height="'500px'"

      v-on:selection="app_selection"
      v-on:detail="app_detail"
      v-on:edit="app_edit"
      v-on:deleted="app_deleted"
      v-on:selected="app_selected">
    </table-chart>
  </div>
</template>

<script>
  import tableChart from './components/TableChart';

  export default {
    name: 'app',
    components: {
      tableChart
    },
    data () {
      return {
        app_helpTitle: '帮助标题',
        app_helpContent: '帮助内容',
        app_tableData: [
          {
            id: 123,
            a: '00234',
            b: '我打打我打我打打',
            c: '3200333',
            d: '42016-05-01',
            e: '王小虎5',
            f: '6用于展示',
            g: '7筛选、对比或其'
          }, {
            id: 23534,
            a: '001',
            b: '是的发送到发送到发',
            c: '3用于展示',
            d: '4王小虎',
            e: '5筛选、对比或其',
            f: '6200333',
            g: '7筛选、对比或其'
          }, {
            id: 3232,
            a: '14534',
            b: '电风扇',
            c: '32016-05-01',
            d: '4上海市普',
            e: '5200333',
            f: '6筛选、对比或其',
            g: '7筛选、对比或其'
          }, {
            id: 4344,
            a: '675',
            b: '鼎折覆餗地方水电费',
            c: '3200333',
            d: '4王小虎',
            e: '52016-05-01',
            f: '6用于展示',
            g: '7筛选、对比或其'
          }, {
            id: 5465,
            a: '54634',
            b: '是的发送到方式',
            c: '3用于展示',
            d: '42016-05-01',
            e: '王小虎5',
            f: '6上海市普',
            g: '7筛选、对比或其'
          }, {
            id: 34326,
            a: '32943',
            b: '打发士大夫似的',
            c: '3筛选、对比或其',
            d: '4王小虎',
            e: '5筛选、对比或其',
            f: '6200333',
            g: '72016-05-01'
          }, {
            id: 7454,
            a: '34433',
            b: '鼎折覆餗地方水电费',
            c: '32016-05-01',
            d: '4200333',
            e: '5王小虎',
            f: '6筛选、对比或其',
            g: '7上海市普'
          }, {
            id: 82344,
            a: '1345',
            b: '电风扇上海市普',
            c: '3用于展示',
            d: '4筛选、对比或其',
            e: '5王小虎',
            f: '6200333',
            g: '7筛选、对比或其'
          }, {
            id: 9345,
            a: '145345',
            b: '二王小虎',
            c: '32016-05-01',
            d: '4筛选、对比或其',
            e: '5上海市普',
            f: '6用于展示',
            g: '7200333'
          }, {
            id: 1232,
            a: '134344',
            b: '维尔维尔而',
            c: '32016-05-01',
            d: '4筛选、对比或其',
            e: '5上海市普',
            f: '6用于展示',
            g: '7200333'
          }, {
            id: 4510,
            a: '564345',
            b: '二位王小虎',
            c: '32016-05-01',
            d: '4筛选、对比或其',
            e: '5上海市普',
            f: '6用于展示',
            g: '7200333'
          }, {
            id: 11546,
            a: '45645345',
            b: '东方闪电王小虎',
            c: '32016-05-01',
            d: '4筛选、对比或其',
            e: '5上海市普',
            f: '6用于展示',
            g: '7200333'
          }
        ],
        app_table_options: [
          {
            prop: 'id',
            label: 'ID',
            sortable: true,
            type: 'number'
          }, {
            prop: 'a',
            label: 'A',
            sortable: true,
            type: 'string'
          }, {
            prop: 'b',
            label: 'B',
            sortable: true
          }, {
            prop: 'c',
            label: 'C',
            sortable: true
          }, {
            prop: 'd',
            label: 'D',
            sortable: true
          }, {
            prop: 'e',
            label: 'E',
            sortable: true
          }, {
            prop: 'f',
            label: 'F',
            sortable: true
          }, {
            prop: 'g',
            label: 'G',
            sortable: true
          }
        ],
        app_chart_options: {
          legend: {
            data: ['进货量', '销量']
          },
          xAxis: {
            data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
          },
          series: [{
            name: '进货量',
            type: 'line',
            data: [6, 23, 38, 13, 10, 20]
          }, {
            name: '销量',
            type: 'bar',
            data: [5, 20, 36, 10, 10, 20]
          }]
        },
        app_selectionKey: 'id',
        app_selectOptions: [{
          title: '吃货',
          label: 'chihuo',
          value: '选项1',
          data: [{
            value: '选项1',
            label: '黄金糕'
          }, {
            value: '选项2',
            label: '双皮奶'
          }, {
            value: '选项3',
            label: '蚵仔煎'
          }, {
            value: '选项4',
            label: '龙须面'
          }, {
            value: '选项5',
            label: '北京烤鸭'
          }]
        }, {
          title: '吃屎',
          label: 'chishi',
          value: '选项2',
          data: [{
            value: '选项1',
            label: '黄金糕'
          }, {
            value: '选项2',
            label: '双皮奶'
          }, {
            value: '选项3',
            label: '蚵仔煎'
          }, {
            value: '选项4',
            label: '龙须面'
          }, {
            value: '选项5',
            label: '北京烤鸭'
          }]
        }]
      };
    },
    methods: {
      app_selection (id) {
        console.log('app', id);
      },
      app_detail (row) {
        console.log('app', row);
      },
      app_edit (row) {
        console.log('app', row);
      },
      app_deleted (row) {
        console.log('app', row);
      },
      app_selected (label, value) {
        console.log('app', label, value);
      }
    }
  };
</script>

<style lang="scss">
  @font-face {
    font-family: 'Open Sans';
    src: url('./assets/fonts/OpenSans-Regular.ttf');
  }
  [class^="el-icon-fa"], [class*=" el-icon-fa"] {
    display: inline-block;
    font: normal normal normal 14px/1 FontAwesome!important;
    font-size: inherit;
    text-rendering: auto;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  };

  $fa-css-prefix: el-icon-fa;
  $fa-font-path: "~@/../node_modules/font-awesome/fonts";

  @import '~@/../node_modules/font-awesome/scss/font-awesome.scss';

  #app {
    font-family: "Open Sans", "Arial", "Microsoft Yahei", "Roboto", "Trebuchet MS", Arial, Helvetica, sans-serif !important;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    font-size: 12px;

    width: 1000px;
    height: 600px;
    margin: 50px auto;
  }
</style>
