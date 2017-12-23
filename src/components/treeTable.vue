<template>
  <div class="tree" style="height:100%">
    <div class="table">
      <div class="table-head table-row">
        <div class="table-td" v-for="(o,i) in column" :key="i" :width="o.width||(column.length/100+'%')" :style="{textAlign:o.textAlign}">
         <template v-if="i===0&&o.check===true">
           <span class="checkbox-box" @click="clid" :class="{'is-checked':parent.check===true,'is-indeterminate':parent.check==='ba'}">
             <span class="checkbox"></span>
             <input type="checkbox" value="" class="checkbox-input">
           </span>
         </template>
         <template v-else>{{o.text}}</template>
        </div>
      </div>
      <div class="table-tbody" @click="clickEvent">
       <treedom id="tree" :treedata="treedata" :column="column" :parent="parent" @detection="detection" :outdata="outdata"></treedom>
      </div>
    </div>
  </div>
</template>
<script>
// params:column 表格列标题Array，treedata 表格数据Array,outdata 输出数据 Array
// column:[{check:true 是否复选框}
// {text:'列标题名称 Strng',
// formater:'替换的内容 String',
// width:'列的宽度 String',
// prop:'对应的字段 String',
// tree:'三角箭头图片在哪个字段出现 Boolean'，
// click:"单击事件 Object key:'选择器(必须用name的值) String' value:'事件函数 Function"}]
import treedom from './treedom'
export default {
  components: {
    treedom
  },
  data () {
    return {
      parent: {check: false}, // 标题的复选框
      clickName: []// 列的单击事件
    }
  },
  props: {
    column: {
      type: Array,
      default: [],
      required: true,
      validator: function (value) {
        return value.length > 0
      }
    },
    treedata: {
      type: Array,
      default: [],
      required: true,
      validator: function (value) {
        return value.length >= 0
      }
    },
    outdata: {
      type: Array,
      default: [],
      required: true,
      validator: function (value) {
        return value.length >= 0
      }
    }
  },
  mounted () {
    this.column.forEach(o => {
      if (o.click) {
        this.clickName = this.clickName.concat(o.click)
      }
    })
  },
  methods: {
    clid () {
      this.parent.check = this.parent.check === 'ba' ? true : !this.parent.check
      this.treedata.forEach(o => {
        this.$set(o, 'check', this.parent.check)
      })
    },
    detection () {
      var bool = this.treedata.every(o => {
        return o.check === true
      })
      var boold = this.treedata.some(o => {
        return o.check
      })
      if (!bool && boold) {
        this.$set(this.parent, 'check', 'ba')
      } else if (!bool) {
        this.$set(this.parent, 'check', false)
      } else if (bool) {
        this.$set(this.parent, 'check', true)
      }
    },
    clickEvent (event) {
      if (this.clickName.indexOf(event.target.name) > -1 || this.clickName.indexOf(event.target.parentNode.name) > -1) {
        this.$emit(event.target.name || event.target.parentNode.name)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tree {
  box-sizing: border-box;
  font-size: 14px;
  font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB,
    Microsoft YaHei, SimSun, sans-serif;
  -webkit-font-smoothing: antialiased;
  position: relative;
}
.tree .table {
  height: 100%;
  overflow: auto;
  white-space: nowrap;
  padding-top: 43px;
  box-sizing: border-box;
}
.tree .table-head {
  color: #909399;
  font-weight: bolder;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  box-sizing: border-box;
}
.tree .table-td,
.table-tbody>>>.table-td {
  border-right: 1px solid #e6ebf5;
  display: inline-block;
  width: 300px;
  text-align: center;
  box-sizing: border-box;
  font-size: 14px;
  padding-left: 10px;
  padding-right: 10px;
  box-sizing: border-box;
}
.tree .table-row,
.table-tbody>>>.table-row {
  font-size: 0;
  border: 1px solid #e6ebf5;
  border-bottom: none;
  height: 43px;
  line-height: 43px;
}
.tree .table-row:nth-last-of-type(1),
.table-tbody>>>.table-row:nth-last-of-type(1) {
  border-bottom: 1px solid #e6ebf5;
}
.tree .table-td:last-of-type(1) {
  border-right: none;
}
.tree .checkbox-box .checkbox,
.table-tbody>>>.checkbox-box .checkbox {
  display: inline-block;
  cursor: pointer;
  position: relative;
  border: 1px solid #d8dce5;
  border-radius: 2px;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  width: 14px;
  height: 14px;
  background-color: #fff;
  z-index: 1;
  -webkit-transition: border-color 0.25s cubic-bezier(0.71, -0.46, 0.29, 1.46),
    background-color 0.25s cubic-bezier(0.71, -0.46, 0.29, 1.46);
  transition: border-color 0.25s cubic-bezier(0.71, -0.46, 0.29, 1.46),
    background-color 0.25s cubic-bezier(0.71, -0.46, 0.29, 1.46);
}
.tree .checkbox-box .checkbox:hover,
.table-tbody>>>.checkbox-box .checkbox:hover {
  border-color: #409eff;
}
.tree .checkbox-box .checkbox-input,
.table-tbody>>>.checkbox-box .checkbox-input {
  display: none;
}
.tree .checkbox-box .checkbox::after,
.table-tbody>>>.checkbox-box .checkbox::after {
  -webkit-box-sizing: content-box;
  box-sizing: content-box;
  content: "";
  border: 1px solid #fff;
  border-left: 0;
  border-top: 0;
  height: 7px;
  left: 4px;
  position: absolute;
  top: 1px;
  -webkit-transform: rotate(45deg) scaleY(0);
  transform: rotate(45deg) scaleY(0);
  width: 3px;
  -webkit-transition: -webkit-transform 0.15s
    cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms;
  transition: -webkit-transform 0.15s cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms;
  transition: transform 0.15s cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms;
  transition: transform 0.15s cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms,
    -webkit-transform 0.15s cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms;
  transition: transform 0.15s cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms,
    -webkit-transform 0.15s cubic-bezier(0.71, -0.46, 0.88, 0.6) 50ms;
  -webkit-transform-origin: center;
  transform-origin: center;
}
.tree .is-checked .checkbox,
.table-tbody>>>.is-checked .checkbox,
.tree .is-indeterminate .checkbox,
.table-tbody>>>.is-indeterminate .checkbox
 {
  border-color: #409eff;
  background-color: #409eff;
}
.tree .is-indeterminate .checkbox::before,
.table-tbody>>>.is-indeterminate .checkbox::before {
  content: "";
  position: absolute;
  display: block;
  background-color: #fff;
  height: 2px;
  transform: scale(0.5);
  left: 0;
  right: 0;
  top: 5px;
}
.tree .is-checked .checkbox::after,
.table-tbody>>>.is-checked .checkbox::after {
  -webkit-transform: rotate(45deg) scaleY(1);
  transform: rotate(45deg) scaleY(1);
}
.tree .table-tbody {
  height: 100%;
  overflow-y: auto;
}
.tree .icon-ji,
.table-tbody>>>.icon-ji {
  width: 6px;
  height: 9px;
  display: inline-block;
  background: url("../assets/san.png") no-repeat;
  transform: rotate(0deg);
  transition: transform 0.3s;
  margin-right: 5px;
}
.tree .cur,
.table-tbody>>>.cur {
  cursor: pointer;
}
.tree .rot,
.table-tbody>>>.rot {
  transform: rotate(90deg);
}
.tree-child,
.table-tbody>>>.tree-child {
  transition: height 0.3s;
  overflow: hidden;
}
</style>
