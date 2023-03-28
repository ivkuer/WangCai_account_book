<template>
  <div>
    <ul class="tabs" :class="{[classPrefix + '-tab']: classPrefix}">
      <li v-for="item in dataSource"
      :key="item.value"
      class="tabs-item"
      :class="{[classPrefix + '-tabs-item']: classPrefix ,selected: item.value === value}"
      @click="select(item)"
      >{{ item.text }}</li>
    </ul>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component, Prop, Watch } from "vue-property-decorator";

type DataSourceItem = {text: string, value: string}

@Component
export default class Tabs extends Vue {
  @Prop(String) readonly value!: string;
  @Prop(String) classPrefix?: string;
 @Prop({required: true, type: Array}) dataSource!: DataSourceItem[]

  select(item: DataSourceItem) {
    this.$emit("update:value", item.value);
  }
}
</script>

<style lang="scss" scoped>
.tabs {
  background: #eee;
  display: flex;
  text-align: center;
  font-size: 24px;
  &-item {
    width: 50%;
    height: 64px;
    display: flex;
  border-radius: 16px;
    justify-content: center;
    align-items: center;
    position: relative;
    transition: all 0.8s;
    &.selected {
      border: 1px solid #000;
      border-radius: 16px;
      background-color: #fff;
    }
  }
}
</style>