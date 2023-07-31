<template>
  <div style="width: 100%; margin: 0 auto">
    <!-- 使用 v-bind和v-on来传递属性和方法-->
    <el-table v-bind="$attrs" v-on="$listeners" height="calc( 100vh - 220px)">
      <el-table-column
        v-if="selectShow"
        type="selection"
        width="55"
      ></el-table-column>
       <el-table-column
        v-if="numberShow"
        label="序号"
        type="index"
        width="60">
       </el-table-column>
      <el-table-column
        v-for="(item, index) in columns"
        :key="index"
        v-bind="item"
      >
        <!-- 使用函数式组件进行dom渲染 -->
        <!-- <template v-if="item.render" v-slot="scope">
        
          <render-dom :render="() => item.render(scope.row)"></render-dom>
        </template> -->
         <!-- 进度条显示 -->
        <template v-if="item.progress==true" v-slot="scope">
          <div>
             <span class="radiusbox" >
                  <span class="radiusspan" :style="{width:(scope.row[item.prop])+'%'}">
                    {{scope.row[item.prop]}}%
                  </span>
                </span>
          </div>
        </template>
        <!-- circle显示 -->
        <template v-else-if="item.circle==true" v-slot="scope">
          <div>
              <span v-if="scope.row[item.prop]==1" class="circlespan"></span>
              <span v-else-if="scope.row[item.prop]==2" class="circlespan circle-red"></span>
              <span v-else  class="circlespan  circle-gray"></span>
            </div>
        </template>
        <template v-else-if="item.onClick==true" v-slot="scope">
          <div>
              <span @click="myClick(scope.row)" style="cursor: pointer;color:#02A7F0">{{scope.row[item.prop]}}</span>
            </div>
        </template>
           <template v-else-if="item.switch==true" v-slot="scope">
            <div>
              <el-switch
                v-model="scope.row[item.prop]"
                active-text="ON"
                active-value="1"
                inactive-value="0"
                inactive-text="OFF">
              </el-switch>
            </div>
        </template>

        <template v-else-if="item.operation==true" v-slot="scope">
          <div>
             <el-button
          size="mini"
          type="primary"
          @click="rowEdit(scope.$index, scope.row)">报修</el-button>
            </div>
        </template>



      </el-table-column>
    </el-table>
    <div class="table-paging-page">
      <el-pagination
        v-if="pagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pagination.pageNum"
        :page-sizes="pagination.pageSizes"
        :page-size="pagination.pageSize"
        v-bind="$attrs"
        layout="total, sizes, prev, pager, next"
        :total="pagination.total"
      >
      </el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  name: "ele-table",
  components: {
    // 函数式组件注册
    renderDom: {
      functional: true,
      props: {
        render: Function,
      },
      render(createElement, renDom) {
        return <div>{renDom.props.render()}</div>;
      },
    },
  },
  props: {
    columns: {
      type: Array,
      default: () => [],
    },
    pagination: Object,
    //多选
    selectShow: {
      type: Boolean,
      default: false,
    },
    //序号
    numberShow:{
      type:Boolean,
      default:false
    }
  },
  data() {
    return {};
  },
  methods: {
    // pageSize 改变时会触发
    handleSizeChange(args) {
      this.$emit("size-change", args);
    },
    // currentPage 改变时会触发
    handleCurrentChange(val) {
      this.$emit("page-change", val);
    },
    // 点击单元格
    myClick(val){
        this.$emit("click-cell",val)
    },
    // 点击一行数据
    rowEdit(index,row){
       this.$emit("click-row",row)
    }

  },
};
</script>

<style scoped>
.table-paging-page {
  margin-top: 20px;
}
</style>