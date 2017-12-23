<template>
  <div id="app" style="height:100%">
    {{outdata}}
    <treeTable :column="column" :treedata="treedata" @add="add" :outdata="outdata"/>
  </div>
</template>

<script>
import treeTable from './components/treeTable'
import treedata from './components/tree'
export default {
  name: 'app',
  data () {
    return {
      column: [
        {check: true, width: '50px'},
        {text: '序号', prop: 'index', width: '100px'},
        {text: '知识点名称', prop: 'name', tree: true, textAlign: 'left'},
        {text: '操作',
          width: '400px',
          formater: '<button name=\'add\' type=\'button\' style=\'width:216px\' class=\'el-button el-button--warning el-button--mini is-plain\'><i class=\'el-icon-plus\'></i><span>新增下级</span></button> <button name=\'edit\' type=\'button\' class=\'el-button el-button--success el-button--mini is-plain\'><i class=\'el-icon-edit\'></i><span>编辑</span></button>',
          click: ['add']
        }
      ],
      treedata: treedata,
      outdata: []
    }
  },
  components: {
    treeTable
  },
  methods: {
    add () {
      alert(11)
    }
  }
}
</script>
<style>
*{
  padding: 0;
  margin: 0;
}
</style>
