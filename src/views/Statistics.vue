<template>
  <Layout>
    <Tabs :data-source="recordTypeList" classPrefix="type" :value.sync="type" />
    <ol v-if="groupList.length > 0">
      <li v-for="group, index in groupList" :key="index">
        <h3 class="title">{{ beautify(group.title) }}
        </h3>
        <ol>
          <li v-for="item, index in group.items" :key="index" class="record">
            <span>{{ tagString(item.tags) }}</span>
            <span class="notes">{{ item.notes }}</span>
            <span>￥{{ item.amount }} </span>
          </li>
        </ol>
      </li>
    </ol>
    <div v-else class="noResult">
      目前没有相关记录
    </div>
    <div id="chart"></div>
  </Layout>
</template>

<script lang="ts">
import { Component } from "vue-property-decorator";
import Vue from "vue";
import Tabs from "@/components/Tabs.vue";
import recordTypeList from "@/constants/recordTypeList";
import dayjs from "dayjs";
import clone from "@/lib/clone";
import * as echarts from 'echarts';
@Component({
  components: { Tabs },
})
export default class Statistics extends Vue {
  tagString(tags: Tag[]) {
    return tags.length === 0 ? "无" : tags.map(t => t.name).join("，");
  }

  beautify(string: string) {
    const day = dayjs(string);
    const now = dayjs();
    if (day.isSame(now, "day")) {
      return "今天";
    } else if (day.isSame(now.subtract(1, "day"), "day")) {
      return "昨天";
    } else if (day.isSame(now.subtract(2, "day"), "day")) {
      return "前天";
    } else if (day.isSame(now, "year")) {
      return day.format("M月D日");
    } else {
      return day.format("YYYY年M月D日");
    }
  }

  get recordList() {
    return (this.$store.state as RootState).recordList;
  }
  data: {value?: number, name: string}[] = []
  get groupList() {
    const { recordList } = this;
    if (recordList.length === 0) {
      return [] as Result;
    }

    const newList = clone(recordList).filter(r => r.type === this.type).sort(
      (a, b) => dayjs(b.createdAt).valueOf() - dayjs(a.createdAt).valueOf()
    );
    if (newList.length === 0) {
      return [] as Result;
    }
    type Result = { title: string, total?: number, items: RecordItem[] }[]
    const result: Result = [
      {
        title: dayjs(newList[0].createdAt).format("YYYY-MM-DD"),
        items: [newList[0]],
      },
    ];
    for (let i = 1; i < newList.length; i++) {
      const current = newList[i];
      const last = result[result.length - 1];
      if (dayjs(last.title).isSame(dayjs(current.createdAt), 'day')) {
        last.items.push(current);
      } else {
        result.push({
          title: dayjs(current.createdAt).format("YYYY-MM-DD"),
          items: [current],
        });
      }
    }
    result.forEach(group => {
      group.total = group.items.reduce((sum, item) =>
        sum + item.amount
        , 0)
    })
    this.data = []
    result.forEach(item => {
      this.data.push({value: item.total, name: this.beautify(item.title)})
    })
    return result;
  }
  beforeCreate() {
    this.$store.commit("fetchRecords");
  }
  type = "-";
  recordTypeList = recordTypeList;

  created() {
    let chartDom = document.getElementById('chart') as HTMLDivElement;
  }
  
  mounted() {
    let chartDom = document.getElementById('chart') as HTMLDivElement;
    let myChart = echarts.init(chartDom);
    let option;

    option = {
      tooltip: {
    trigger: 'item'
  },
      series: [
        {
          type: 'pie',
          radius: '90%',
          top: 0,
          left: 0,
          buttom: 0,
          right: 0,
          label: {
           show: false,
           position: 'center'
        },
      emphasis: {
        label: {
          show: true,
          fontSize: 40,
          fontWeight: 'bold'
        }
      },
      labelLine: {
        show: false
      },
          data: this.data,
        }
      ]
    };

    option && myChart.setOption(option);
  }

  updated() {
    let chartDom = document.getElementById('chart') as HTMLDivElement;
    let myChart = echarts.init(chartDom);
    let option;

    option = {
      tooltip: {
    trigger: 'item'
  },
      series: [
        {
          type: 'pie',
          radius: '90%',
          top: 0,
          left: 0,
          buttom: 0,
          right: 0,
          label: {
           show: false,
           position: 'center'
        },
      emphasis: {
        label: {
          show: true,
          fontSize: 40,
          fontWeight: 'bold'
        }
      },
      labelLine: {
        show: false
      },
          data: this.data,
        }
      ]
    };

    option && myChart.setOption(option);
  }

}
</script>

<style scoped lang="scss">
#chart {
  height: 200px;
  width: 100%;
}

.noResult {
  padding: 16px;
  text-align: center;
}

::v-deep {
  .type-tabs-item {
    background: transparent;
    


    &.selected {
      background-color: #fff;

      &::after {
        display: none;
      }
    }
  }

  .interval-tabs-item {
    height: 48px;
    
  }
}

%item {
  padding: 8px 16px;
  line-height: 24px;
  display: flex;
  justify-content: space-between;
  align-content: center;
  
}

.title {
  @extend %item;
}

.record {
  background: #e3eeff ;
  @extend %item;
}

.notes {
  margin-right: auto;
  margin-left: 16px;
  color: #999;
}</style>
