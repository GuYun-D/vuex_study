<template>
  <div id="app">
    <a-input
      placeholder="请输入任务"
      class="my_ipt"
      :value="inputValue"
      @change="handleInputChange"
    />
    <a-button type="primary" @click="addItemToList">添加事项</a-button>

    <a-list bordered :dataSource="infolist" class="dt_list">
      <a-list-item slot="renderItem" slot-scope="item">
        <!-- 复选框 -->
        <a-checkbox
          :checked="item.done"
          @change="
            (e) => {
              cbstatusChange(e, item.id)
            }
          "
          >{{ item.info }}</a-checkbox
        >
        <!-- 删除链接 -->
        <a slot="actions" @click="removeItemById(item.id)">删除</a>
      </a-list-item>

      <!-- footer区域 -->
      <div slot="footer" class="footer">
        <!-- 未完成的任务个数 -->
        <span> {{unDoneLength}} 条未完成</span>
        <!-- 操作按钮 -->
        <a-button-group>
          <a-button
            :type="viewKey === 'all' ? 'primary' : 'default'"
            @click="changeList('all')"
            >全部</a-button
          >
          <a-button
            :type="viewKey === 'undone' ? 'primary' : 'default'"
            @click="changeList('undone')"
            >未完成</a-button
          >
          <a-button
            :type="viewKey === 'done' ? 'primary' : 'default'"
            @click="changeList('done')"
            >已完成</a-button
          > </a-button-group
        >
        <!-- 把已经完成的任务清空 -->
        <a @click="clean">清除已完成</a>
      </div>
    </a-list>
  </div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'

export default {
  name: 'app',
  data() {
    return {}
  },

  created() {
    this.$store.dispatch('getList')
  },

  computed: {
    // ...mapState(['list', 'inputValue', 'viewKey']),
    ...mapState(['inputValue', 'viewKey']),
    ...mapGetters(['unDoneLength', 'infolist']),
  },

  methods: {
    // 监听文本框内容变化
    handleInputChange(e) {
      console.log(e.target.value)
      // 当文本变化，将变化后的值赋给vuex
      this.$store.commit('setInputValue', e.target.value)
    },

    // 向列表中添加项
    addItemToList() {
      if (this.inputValue.trim().length <= 0) {
        return this.$message.warning('请输入内容')
      }

      this.$store.commit('addItem')
    },

    // 删除对应项
    removeItemById(id) {
      // console.log(id);
      this.$store.commit('removeItem', id)
    },

    // 复选框状态发生改变触发
    cbstatusChange(e, id) {
      // 获取选中状态
      // console.log(e.target.checked);
      // console.log(id);
      const param = {
        id: id,
        status: e.target.checked,
      }

      this.$store.commit('changeStatus', param)
    },

    // 清除已完成
    clean() {
      this.$store.commit('cleanDone')
    },

    // 切换列表
    changeList(key) {
      // console.log(key);
      this.$store.commit('changeViewKey', key)
    },
  },
}
</script>

<style scoped>
#app {
  padding: 10px;
}

.my_ipt {
  width: 500px;
  margin-right: 10px;
}

.dt_list {
  width: 500px;
  margin-top: 10px;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
