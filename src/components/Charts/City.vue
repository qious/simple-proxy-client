<template>
  <div class="city" v-loading="loading">
    <e-charts :options="option" v-if="!loading && option"></e-charts>
  </div>
</template>

<script>
import ECharts from 'vue-echarts/components/ECharts.vue'
import 'echarts/lib/chart/pie'
import 'echarts/lib/component/title'
import 'echarts/lib/component/tooltip'
import {getColors} from '../../libs/utils'

export default {
  props: ['data', 'loading'],

  components: {
    ECharts
  },

  data () {
    return {
      option: null
    }
  },

  created () {
    this.update()
  },

  methods: {
    update () {
      if (this.loading) {
        return false
      }

      let data = []
      for (let item of this.data) {
        if (item.city === 'UNKNOWN') {
          continue
        }
        data.push({
          value: item.count,
          name: item.city
        })
      }
      data.sort((a, b) => b.value - a.value)
      if (!data.length) {
        data.push({value: 1, name: '暂无数据'})
      }

      this.option = {
        title: {
          text: '城市占比',
          x: 'center'
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a} <br/>{b} : {c} 次 ({d}%)'
        },
        color: getColors(),
        series: [
          {
            name: '访问来源',
            type: 'pie',
            radius: '50%',
            center: ['50%', '60%'],
            data: data.slice(0, 30),
            itemStyle: {
              emphasis: {
                shadowBlur: 10,
                shadowOffsetX: 0,
                shadowColor: 'rgba(0, 0, 0, 0.5)'
              }
            }
          }
        ]
      }
    }
  },

  watch: {
    data () {
      this.update()
    }
  }
}

</script>

<style lang="less" scoped>
.city {
  width: 100%;
  height: 400px;
  margin-top: 20px;
  position: relative;
  .echarts {
    width: 100%;
    height: 100%;
    margin: auto;
  }
}
</style>
