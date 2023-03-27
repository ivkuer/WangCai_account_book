<template>
    <Layout>
      <Tabs :data-source="recordTypeList" classPrefix="type" :value.sync="type" />
      <Tabs :data-source="intervalList"
      classPrefix="interval"
      :value.sync="interval" />
    </Layout>
</template>

<script lang="ts">
import Types from '@/components/Money/Types.vue';
import { Component } from 'vue-property-decorator';
import Vue from 'vue'
import Tabs from '@/components/Tabs.vue';
import intervalList from '@/constants/intervalList';
import recordTypeList from '@/constants/recordTypeList';
@Component({
  components: { Types, Tabs }
}
)
export default class Statistics extends Vue {
  type = '-'
  interval = 'day'
  intervalList = intervalList
  recordTypeList = recordTypeList
};
</script>

<style scoped lang="scss">
::v-deep .type-tab-item {
  background-color: #fff;
  &.selected {
    background: #c4c4c4;
    &::after {
      display: none;
    }
  }
}
</style>
