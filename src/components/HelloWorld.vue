<template>
  <div
    ref="VirtualList"
    class="virtual-list"
    :style="{height:listHeight+'px'}"
    @scroll="scrollListener"
  >
    <div
      ref="scrollBar"
      class="scroll-bar"
      :style="{height:scrollBarHeight+'px'}"
    />
    <ul ref="virtual-ul" class="virtual-ul">
      <li v-for="val in showList" :key="val">{{ val }}</li>
    </ul>
  </div>
</template>

<script>
import { reactive, toRefs, computed, onMounted, } from "vue"
export default {
  name: 'VirtualList',

  setup() {

    const state = {
      list: [], // 列表数据
      itemHeight: 30, // 每一列的高度
      showNum: 10, // 显示几条数据
      start: 0, // 开始索引
      end: 10, // 结束索引
      pageNum: 0, // 页数
      limit: 20 // 每页条数
    }

    onMounted(() => {
      getData()
    })

    // 显示的数据
    // TODO: 执行了一次？
    // const showList = computed(() => state.list.slice(state.start, state.end))
    const showList = computed(() => {
      console.log("!!!!!!!")
      console.log(state.start)
      console.log(state.end)
      console.log(state.list)
      console.log(state.list.slice(state.start, state.end))

      return state.list.slice(state.start, state.end)
    })

    // 计算滚动容器高度
    const listHeight = computed(() => state.itemHeight * state.showNum)
    
    // 计算滚动容器高度
    const scrollBarHeight = computed(() => state.itemHeight * state.list.length)

    // 获取数据
    function getData() {
      const list = []
      for (let i = 0; i < state.limit; i++) {
        const index = state.pageNum * state.limit + i
        list.push('item ' + index)
      }

      console.log(list)
      state.pageNum++
      state.list = state.list.concat(list)
      console.log(state.list)
    }

    // 滑动
    function scrollListener() {
      const scrollTop = state.$refs.VirtualList.scrollTop
      state.start = Math.floor(scrollTop / state.itemHeight)
      state.end = state.start + state.showNum
      // 绝对定位对相对定位的偏移量
      state.$refs.list.style.top = state.start * state.itemHeight + 'px'

      // 触底加载
      state.listHeight + scrollTop >= state.scrollBarHeight ? getData() : void 0
    }


    return {
      ...toRefs(state),
      showList,
      listHeight,
      scrollBarHeight,
      getData,
      scrollListener,
    }
  }

}
</script>


<style scoped>
.virtual-list::-webkit-scrollbar {
  width: 4px
}
.virtual-list::-webkit-scrollbar-thumb {
  width: 5px;
  background-color: #dde1e4
}
.virtual-list {
  position: relative;
  overflow-y: scroll;
  width: 200px;
  margin: 0 auto;
  box-sizing: border-box;
}
.virtual-ul {
  width: 100%;
  position: absolute;
  top: 0;
  left: 0;
  padding: 0;
  margin: 0;
}
.virtual-ul li {
  line-height: 30px;
  margin-bottom: 10px;
  background: #41b883;
  color: #fff;
  text-align: center;
}
</style>
