<template>
    <div class="plot-time-series" :id="plotId">
    </div>
</template>

<script>
import Vue from 'vue'
import BootstrapVue from 'bootstrap-vue'
import $ from 'jquery'
import echarts from 'echarts'
import _ from 'lodash'
import StringUtils from './stringUtils.js'

export default {
    props: {
        title: {
            required: true
        },
        yAxis: {
            required: true
        },
        yMax: {
            required: false
        },
        series: {
            default: () => [],
        }
    },
    data() {
        return {
            plotId: "plot-" + StringUtils.randomUUID()
        }
    },
    mounted() {
        // resize plot according to window height, cap at 400px
        $("#"+this.plotId).height(Math.min(Math.max(260, $(window).height()*0.34), 400))
        this.myChart = echarts.init(document.getElementById(this.plotId));
        var z = 2   // z value for series
        // draw chart
        this.myChart.setOption({
            title: {
                text: this.title,
                x: 'center'
            },
            tooltip: {
                trigger: 'axis'
            },
            legend: {
                data: this.series.map(s => s.name),
                top: '26px'
            },
            grid: {
                left: '100px',
                right: '80px',
                top: '60px',
                bottom: '50px',
                containLabel: false
            },
            xAxis: {
                type: 'time'
            },
            yAxis: {
                type: 'value',
                name: this.yAxis,
                max: this.yMax
            },
            series: this.series.map(s => ({
                animationDuration: function(idx) { return 500},
                color: s.color,
                name: s.name,
                type: 'line',
                symbolSize: 0,
                hoverAnimation: false,
                stack: s.stack,
                areaStyle: { normal: { opacity: (s.opacity !== undefined) ? s.opacity : 1 } },
                z: z++,
                data: _.zip(s.time, s.values).map(v => ({ value: v }))
            }))
        });
    },
    destroyed() {
        this.myChart && this.myChart.dispose()
    }
}
</script>

<style lang="scss">
</style>
