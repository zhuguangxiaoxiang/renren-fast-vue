<template>
  <div>
    <el-tree :data="menus" :props="defaultProps"
             :expand-on-click-node="false"
             show-checkbox node-key="catId"
             :default-expanded-keys="expandedKey">
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="node.level <= 2"
            type="text"
            size="mini"
            @click="() => append(data)">
            Append
          </el-button>
          <el-button
            v-if="node.childNodes.length === 0"
            type="text"
            size="mini"
            @click="() => remove(node, data)">
            Delete
          </el-button>
        </span>
      </span>
    </el-tree>
  </div>
</template>
<script>
//这里可以导入其他文件（比如：组件，工具 js，第三方插件 js，json文件，图片文件等等）,
//例如：import 《组件名称》 from '《组件路径》';
export default {
//import 引入的组件需要注入到对象中才能使用
  components: {},
  props: {},
  data () {
    //这里存放数据
    return {
      menus: [],
      expandedKey: [],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  //计算属性 类似于 data 概念
  computed: {},
  //监控 data 中的数据变化,
  watch: {},
  //方法集合
  methods: {
    getMenus () {
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({data}) => {
        console.log('成功获取到菜单数据...', data.data)
        this.menus = data.data
      })
    },

    append (data) {
      console.log('append', data)
    },

    remove (node, data) {
      var ids = [data.catId]
      this.$confirm(`是否删除【${data.name}】菜单?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/product/category/delete'),
          method: 'post',
          data: this.$http.adornData(ids, false)
        }).then(({data}) => {
          this.$message({
            message: '菜单删除成功',
            type: 'success'
          })
          console.log('删除成功...', '当前id为' + ids, '父ID为' + node.data.parentCid)
          //刷新出新的菜单
          this.getMenus()
          //设置需要默认展开的菜单
          this.expandedKey = [node.data.parentCid]
        })
        console.log('remove', node, data)
        console.log('父ID',node.data.parentCid)
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
      console.log('remove', node, data)
    },
  },
  //生命周期 - 创建完成（可以访问当前 this 实例）
  created () {
    this.getMenus()
  },
  //生命周期 - 挂载完成（可以访问 DOM 元素）,
  mounted () {
  },
  //生命周期 - 创建之前
  beforeCreate () {
  },
  //生命周期 - 挂载之前
  beforeMount () {
  },
  //生命周期 - 更新之前
  beforeUpdate () {
  },
  //生命周期 - 更新之后
  updated () {
  },
  //生命周期 - 销毁之前
  beforeDestroy () {
  },
  //生命周期 - 销毁完成
  destroyed () {
  },
  //如果页面有 keep-alive 缓存功能，这个函数会触发
  activated () {
  }
}
</script>
<style scoped>
</style>
