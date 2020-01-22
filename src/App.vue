<template>
  <div id="app">
    <a-input placeholder="请输入任务" class="my_ipt"
    :value="inputValue" @change="handleInputChange"/>
    <a-button type="primary" @click="addItemToList">添加事项</a-button>
    <a-list bordered :dataSource="infoList" class="dt_list">
      <a-list-item slot="renderItem" slot-scope="item">
        <!-- 复选框 -->
        <a-checkbox :checked="item.done"
        @change="cbStateChanged(item.id,$event)">{{item.info}}</a-checkbox>
        <!-- 删除链接 -->
        <a slot="actions" @click="removeItemById(item.id)">删除</a>
      </a-list-item>
        <!-- footer区域 -->
        <div slot="footer" class="footer">
          <!-- 使用映射好的计算属性展示剩余项 -->
          <!-- 未完成的任务数 -->
          <span>{{unDoneLength}}条剩余</span>
          <!-- 操作按钮 -->
          <a-button-group>
            <a-button type="viewKey==='all'?'primary':'default'" 
            @click="changeList('all')">全部</a-button>
            <a-button type="viewKey==='all'?'primary':'default'" 
            @click="changeList('undone')">未完成</a-button>
            <a-button type="viewKey==='all'?'primary':'default'" 
            @click="changeList('done')">已完成</a-button>
          </a-button-group>
          <!-- 把已经完成的任务清空 -->
          <a @click="clean">清除已完成</a>
        </div>
    </a-list>
  </div>
</template>

<script>
import { mapState,mapGetters } from 'vuex'

export default {
  name: 'app',
  data(){
    return {
      // list:[]
    }
  },
  created(){
    this.$store.dispatch('getList')
  },
  methods:{
    handleInputChange(e){
      this.$store.commit('setInputValue',e.target.value)
    },
    addItemToList(){
      if(this.inputValue.trim().length<=0){
        return this.$message.warning('文本框不能为空')
      }
      this.$store.commit('addItem')
    },
    removeItemById(id){
      this.$store.commit('removeItem',id)
    },
    cbStateChanged(id,e){
      const param = {
        id:id,
        status:e.target.checked
      }
      this.$store.commit('changeStatus',param)
    },
    clean(){
      //清楚已经完成的事项
      this.$store.commit('cleanDone')
    },
    changeList(key){
      this.$store.commit('changeKey',key)
    }
  },
  computed:{
    ...mapState(['list','inputValue','viewKey']),
    ...mapGetters(['unDoneLength','infoList'])
  }
}
</script>

<style>
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
