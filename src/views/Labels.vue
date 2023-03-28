<template>
  <Layout>
    <div class="tags">
      <router-link class="tag"
                   v-for="tag in tags" :key="tag.id"
                   :to="`/labels/edit/${tag.id}`">
        <span>{{tag.name}}</span>
        <Icon name="right"/>
      </router-link>
    </div>
    <div class="createTag-wrapper">
      <Button class="createTag"
              @click="createTag">
        新建标签
      </Button>
    </div>
  </Layout>
</template>

<script lang="ts">

  import {Component} from 'vue-property-decorator';
  import Button from '@/components/Button.vue';
import { mixins } from 'vue-class-component';
import TagHelper from '@/mixin/TagHelper'

  @Component({
    components: {Button},
  })
  export default class Labels extends mixins(TagHelper) {
    get tags() {
      return this.$store.state.tagList
    }
    created() {
      this.$store.commit('fetchTags')
    }

    createTag() {
      const name = window.prompt('请输出标签名');
      if (!name) {
        return window.alert('标签名不能为空')
      }
      this.$store.commit('createTag', name)
    }
  }
</script>

<style lang="scss" scoped>
  .tags {
    background: transparent;
    font-size: 16px;
    > .tag {
      min-height: 44px;
      display: flex;
    margin: 8px;
    padding: 10px;
    background-image: linear-gradient(to top, #accbee 0%, #e7f0fd 100%);
    border-radius: 8px;
box-shadow: 0px 0px 2px rgba(0, 0, 0, 0.4);
      align-items: center;
      justify-content: space-between;
      svg {
        width: 18px;
        height: 18px;
        color: #666;
        margin-right: 16px;
      }
    }
  }
  .createTag {
    background: #767676;
    color: white;
    border-radius: 4px;
    border: none;
    height: 40px;
    padding: 0 16px;
    &-wrapper {
      text-align: center;
      padding: 16px;
      margin-top: 44-16px;
    }
  }
</style>

