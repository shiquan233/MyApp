<template>
  <el-row>
    <el-col :span="8" style="padding-right: 8px">
      <el-card class="box-card">
        <div class="user">
          <img src="../assets/images/user.png" alt="" />
          <div class="userinfo">
            <p class="name">Admin</p>
            <p class="access">超级管理员</p>
          </div>
        </div>
        <div class="login-info">
          <p>上次登录时间：<span>2023-5-19</span></p>
          <p>上次登录地点：<span>江苏</span></p>
        </div>
      </el-card>
      <el-card style="margin-top: 16px; height: 368px">
        <el-table :data="tableData" style="width: 100%">
          <el-table-column v-for="(val, key) in tableLabel" :prop="key" :label="val" :key="key" />
        </el-table>
      </el-card>
    </el-col>
    <el-col :span="16" style="padding-left: 8px">
      <div class="num">
        <el-card v-for="item in countData" :key="item.name" :body-style="{ display: 'flex', padding: 0 }">
          <i class="icon" :class="`el-icon-${item.icon}`" :style="{ background: item.color }"></i>
          <div class="detail">
            <p class="price">￥{{ item.value }}</p>
            <p class="desc">{{ item.name }}</p>
          </div>
        </el-card>
      </div>
      <el-card style="height: 224px">
        <!-- 折线图 -->
        <div ref="echarts1" style="height: 224px"></div>
      </el-card>
      <div class="graph">
        <el-card style="height: 208px">
          <div ref="echarts2" style="height: 208px"></div>
        </el-card>
        <el-card style="height: 208px">
          <div ref="echarts3" style="height: 190px"></div>
        </el-card>
      </div>
    </el-col>
  </el-row>
</template>
<script>
import { getData } from '../api'
import * as echarts from 'echarts'
export default {
  data() {
    return {
      tableData: [],
      tableLabel: {
        name: "课程",
        todayBuy: "今日购买",
        monthBuy: "本月购买",
        totalBuy: "总购买",
      },
      countData: [
        {
          name: "今日支付订单",
          value: 1234,
          icon: "success",
          color: "#2ec7c9",
        },
        {
          name: "今日收藏订单",
          value: 210,
          icon: "star-on",
          color: "#ffb980",
        },
        {
          name: "今日未支付订单",
          value: 1234,
          icon: "s-goods",
          color: "#5ab1ef",
        },
        {
          name: "本月支付订单",
          value: 1234,
          icon: "success",
          color: "#2ec7c9",
        },
        {
          name: "本月收藏订单",
          value: 210,
          icon: "star-on",
          color: "#ffb980",
        },
        {
          name: "本月未支付订单",
          value: 1234,
          icon: "s-goods",
          color: "#5ab1ef",
        },
      ],
    };
  },
  mounted() {
    getData().then(({ data }) => {
      const { tableData } = data.data;
      this.tableData = tableData;


      const echarts1 = echarts.init(this.$refs.echarts1);

      var echarts1Option = {};

      const { orderData, userData, videoData } = data.data;
      const xAxis = Object.keys(orderData.data[0]);
      const xAxisData = {
        data: xAxis,
      };
      echarts1Option.xAxis = xAxisData;
      echarts1Option.yAxis = {};
      echarts1Option.legend = xAxisData;
      echarts1Option.series = [];
      xAxis.forEach((key) => {
        echarts1Option.series.push({
          name: key,
          data: orderData.data.map((item) => item[key]),
          type: "line",
        });
      });
      console.log(echarts1Option);
      // 使用刚指定的配置项和数据显示图表。
      echarts1.setOption(echarts1Option);

      // 柱状图
      const echarts2 = echarts.init(this.$refs.echarts2);
      const eachrts2Option = {
        legend: {
          // 图例文字颜色
          textStyle: {
            color: "#333",
          },
        },
        grid: {
          left: "20%",
        },
        // 提示框
        tooltip: {
          trigger: "axis",
        },
        xAxis: {
          type: "category", // 类目轴
          data: userData.map((item) => item.date),
          axisLine: {
            lineStyle: {
              color: "#17b3a3",
            },
          },
          axisLabel: {
            interval: 0,
            color: "#333",
          },
        },
        yAxis: [
          {
            type: "value",
            axisLine: {
              lineStyle: {
                color: "#17b3a3",
              },
            },
          },
        ],
        color: ["#2ec7c9", "#b6a2de"],
        series: [
          {
            name: "新增用户",
            data: userData.map((item) => item.new),
            type: "bar",
          },
          {
            name: "活跃用户",
            data: userData.map((item) => item.active),
            type: "bar",
          },
        ],
      };
      echarts2.setOption(eachrts2Option);

      // 饼状图
      const echarts3 = echarts.init(this.$refs.echarts3);
      const eachrts3Option = {
        tooltip: {
          trigger: "item",
        },
        color: [
          "#0f78f4",
          "#dd536b",
          "#9462e5",
          "#a6a6a6",
          "#e1bb22",
          "#39c362",
          "#3ed1cf",
        ],
        series: [
          {
            data: videoData,
            type: "pie",
          },
        ],
      };
      echarts3.setOption(eachrts3Option);
    });
    getData().then((data) => {
      // console.log(data)
    })
  },
};
</script>
<style lang="less" scoped>
* {
  margin: 0;
}

.user {
  padding-bottom: 16px;
  margin-bottom: 16px;
  border-bottom: 1px solid #ccc;
  display: flex;
  align-items: center;

  img {
    margin-right: 32px;
    width: 120px;
    height: 120px;
    border-radius: 50%;
  }

  .userinfo {
    .name {
      font-size: 25.6px;
      margin-bottom: 8px;
    }

    .access {
      color: #999999;
    }
  }
}

.login-info {
  p {
    line-height: 22.4px;
    font-size: 11.2px;
    color: #999999;

    span {
      color: #666666;
      margin-left: 48px;
    }
  }
}

.num {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;

  .icon {
    width: 64px;
    height: 64px;
    font-size: 24px;
    text-align: center;
    line-height: 64px;
    color: #fff;
  }

  .detail {
    margin-left: 12px;
    display: flex;
    flex-direction: column;
    justify-content: center;

    .price {
      font-size: 24px;
      margin-bottom: 8px;
      line-height: 24px;
      height: 24px;
    }

    .desc {
      font-size: 11.2px;
      color: #999;
      text-align: center;
    }
  }

  .el-card {
    width: 32%;
    margin-bottom: 16px;
  }
}

.graph {
  margin-top: 16px;
  display: flex;
  justify-content: space-between;

  .el-card {
    width: 48%;
  }
}
</style>