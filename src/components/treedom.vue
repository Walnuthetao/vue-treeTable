<template>
    <div class="tree-child" :id="parent.id" :style="{height:(parent.height?parent.height+'px':'auto')}">
      <template v-for="(d,k) in treedata">
           <div class="table-row"  :key="d.id">
             <template v-for="(o,i) in column">
              <div class="table-td"  :key="i" :class="{'cur':o.tree}" @click="treeClick(d,o.tree)" :style="{textAlign:o.textAlign}">
                <template v-if="i===0&&o.check===true">
                  <span class="checkbox-box" @click="checkClick(d)" :class="{'is-checked':d.check===true,'is-indeterminate':d.check==='ba'}">
                    <span class="checkbox"></span>
                    <input type="checkbox" value="" class="checkbox-input">
                  </span>
                </template>
                <div v-else-if="o.formater" v-html="o.formater"></div>
                <template v-else>
                  <span :style="{paddingLeft:o.tree&&d.left+'px'}">
                    <span v-if="d['children']&&o.tree" class="icon-ji" :class="{'rot':(d.height!=null&&d.height!=0.5)}"/>
                    {{d[o.prop]||'&nbsp'}}</span>
                  </template>
                </div>
             </template>
        </div>
        <treedom v-if="d['children']" :parent="d" :treedata="d['children']" :column="column" :key="k" @detection="detection" :outdata="outdata"></treedom>
        </template>
        </div>
</template>
<script>
import treedom from './treedom'
export default{
  name: 'treedom',
  components: {
    treedom
  },
  data () {
    return {
      time: ''
    }
  },
  props: {
    treedata: {
      type: Array,
      default: [],
      required: true,
      validator: function (value) {
        return value.length >= 0
      }
    },
    column: {
      type: Array,
      default: [],
      required: true,
      validator: function (value) {
        return value.length > 0
      }
    },
    parent: {
      type: Object,
      default: {}
    },
    outdata: {
      type: Array,
      default: [],
      required: true
    }
  },
  beforeMount () {
    this.treedata.forEach(o => {
      this.$set(o, 'height', 0.5)
      var left = 0
      if (typeof this.parent.left === 'number') {
        left = this.parent.left + 15
      }
      this.$set(o, 'left', left)
    })
  },
  methods: {
    treeClick (d, bool) {
      if (bool) {
        if (d.height !== 0.5) {
          d.oldHeight = d.height = document.querySelector('#' + d.id).offsetHeight
        }
        this.$nextTick(() => {
          if (d.height === 0.5) {
            d.height = d.oldHeight || d.children.length * 44.5
          } else {
            setTimeout(() => {
              if (d.height !== 0.5) {
                d.height = 0.5
              }
            }, 10)
          }
          this.$nextTick(() => {
            setTimeout(() => {
              if (d.height !== 0.5) {
                d.height = ''
              }
            }, 300)
          })
        })
      }
    },
    checkClick (d) {
      this.$set(d, 'check', d.check === 'ba' ? true : !d.check)
      if (typeof d['children'] === 'undefined') {
        let array = this.outdata.filter((o, k) => {
          d.check === false && o === d && this.outdata.splice(k, 1)
          return o === d
        })
        d.check === true && array.length === 0 && this.outdata.push(d)
      }
      this.detection(true)
      this.$emit('detection')
    },
    detection (val) {
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
      if (!val) {
        this.$emit('detection')
      }
    }
  },
  watch: {
    'parent.check' (val) {
      if (this.parent.check !== 'ba') {
        this.treedata.forEach(o => {
          this.$set(o, 'check', val)
          let array = this.outdata.filter((d, k) => {
            val === false && o === d && this.outdata.splice(k, 1)
            return o === d
          })
          val === true && array.length === 0 && this.outdata.push(o)
        })
      }
    }
  }
}
</script>
<style>
.bod{
  border-bottom: 1px solid #e6ebf5;
}
</style>

