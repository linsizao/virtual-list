<template>
  <div
    ref="virtualList"
    class="virtual-list"
    :style="{height:listHeight+'px'}"
    @scroll="scrollListener"
  >
    <div
      ref="scrollBar"
      class="scroll-bar"
      :style="{height:scrollBarHeight+'px'}"
    />
    <ul
      ref="virtualUl"
      class="virtual-ul"
    >
      <li
        v-for="val in showList"
        :key="val"
        :style="{'margin-bottom': itemGap+'px'}"
      >{{ val }}</li>
    </ul>
  </div>
</template>

<script>
import { reactive, toRefs, ref, computed, onMounted } from "vue"
export default {
  name: 'VirtualList',

  setup() {

    const state = reactive({
      list: [], // 列表数据
      itemHeight: 30, // 每一列的高度
      itemGap: 10,
      showNum: 10, // 显示几条数据
      start: 0, // 开始索引
      end: 10, // 结束索引
      pageNum: 0, // 页数
      limit: 20 // 每页条数
    })

    const virtualList = ref(null)
    const virtualUl = ref(null)

    onMounted(() => {
      // getData()
      scrollListener()
    })

    // 显示的数据
    const showList = computed(() => state.list.slice(state.start, state.end))

    // 计算滚动容器高度
    const listHeight = computed(() => (state.itemHeight + 10) * state.showNum)
    
    // 计算滚动容器高度
    const scrollBarHeight = computed(() => state.itemHeight * state.list.length)

    // 获取数据（模拟无限数据）
    function getData() {
      const list = []
      for (let i = 0; i < state.limit; i++) {
        const index = state.pageNum * state.limit + i
        list.push('item ' + index)
      }

      state.pageNum++
      state.list = state.list.concat(list)
    }

    // 滑动
    function scrollListener() {
      const scrollTop = virtualList.value.scrollTop
      const floor = Math.floor(scrollTop / (state.itemHeight + state.itemGap))
      // 设置起始和结束锚点
      state.start = (floor > state.showNum / 2) ? (floor - state.showNum / 2) : 0
      state.end = floor + state.showNum + state.showNum / 2
      // 绝对定位对相对定位的偏移量
      virtualUl.value.style.top = state.start *  (state.itemHeight + state.itemGap) + 'px'

      // 触底加载
      listHeight.value + scrollTop >= scrollBarHeight.value ? getData() : void 0
    }


    return {
      ...toRefs(state),
      virtualList,
      virtualUl,
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
.virtual-list::-webkit-scrollbar,.virtual-list::-webkit-scrollbar-thumb {
  width: 0px;
}
.virtual-list {
  position: relative;
  overflow-y: scroll;
  width: 300px;
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
  background: #41b883;
  color: #fff;
  text-align: center;
}
</style>
