<template>
  <div
    class="ECharts"
    :style="{ height: e_height }">
  </div>
</template>

<script>
  const echarts = require('echarts');
  export default {
    name: 'ECharts',
    props: {
      height: {
        type: String,
        default: ''
      },
      options: {
        type: Object,
        default: {}
      }
    },
    data () {
      return {
        /* -----------------输入----------------- */
        e_height: '',
        e_options: {}

        /* -----------------内部----------------- */
      };
    },
    computed: {},
    beforeMount () {
      // 初始化数据
      this.e_height = this.height;
      this.e_options = this.options;
    },
    mounted () {
      // 初始化echarts
      let chart = echarts.init(this.$el);
      chart.setOption(this.e_options);
    },
    methods: {
      /* -----------------输出----------------- */

      /* -----------------内部----------------- */
    },
    destroyed () {}
  };
</script>

<style lang="scss" scoped>
.ECharts {
  width: 100%;
  font-size: 12px;
  line-height: 16px;
  background: #fff;
}
</style>
