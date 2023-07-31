<template>
  <div class="organization">
    <div>
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="部门名称">
          <el-input
            v-model="formInline.name"
            placeholder="请输入部门名称"
          ></el-input>
        </el-form-item>

        <el-form-item label="状态">
          <el-select v-model="formInline.state" placeholder="组织状态">
            <el-option label="正常" value="1"></el-option>
            <el-option label="停用" value="0"></el-option>
          </el-select>
        </el-form-item>
        <!-- <el-form-item label="创建时间">
          <el-date-picker
            v-model="valueDate"
            type="daterange"
            range-separator="-"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          >
          </el-date-picker>
        </el-form-item> -->
        <el-form-item>
          <el-button type="primary" @click="searchClick">查询</el-button>
        </el-form-item>
      </el-form>
      <div>
        <el-row>
          <el-button
            size="small"
            type="primary"
            icon="el-icon-plus"
            @click="addRow"
            >新增</el-button
          >
        </el-row>
      </div>
    </div>
    <div class="organization-table">
      <el-table
        ref="multipleTable"
        :data="tableData"
        tooltip-effect="dark"
        row-key="id"
        border
        :default-expand-all="false"
        :tree-props="{ children: 'children', hasChildren: 'hasChildren' }"
        style="width: 100%"
        height="calc(100vh - 260px)"
      >
        <el-table-column prop="name" label="部门名称"> </el-table-column>
        <el-table-column prop="order" label="排序"> </el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <span class="stateTip">{{
              scope.row.state == 0 ? "停用" : "正常"
            }}</span>
            <!-- <el-switch
              v-model="scope.row.state"
              active-value="1"
              inactive-value="0"
              @change="switchChange($event, scope.row)"
            >
            </el-switch> -->
          </template>
         
        </el-table-column>
         <!-- <el-table-column prop="linkMen" label="负责人"> </el-table-column> -->
        <el-table-column prop="date" label="创建时间" show-overflow-tooltip>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <span class="edits" @click="editClick(scope.row)">编辑</span>
            <span
              class="deleteds"
              @click="deletedClick(scope.$index, scope.row)"
              >删除</span
            >
          </template>
        </el-table-column>
      </el-table>
    </div>
    <el-dialog
      :title="dialogTitle"
      width="550px"
      :visible.sync="dialogFormVisible"
    >
      <el-form ref="ruleForm" :model="dialogForm" :rules="rules">
        <el-form-item
          label="上级部门"
          prop="name"
          :label-width="formLabelWidth"
        >
          <el-select
            v-model="statusData"
            placeholder="上级部门"
            style="width: 100%"
          >
            <el-option :value="statusValueData" style="height: auto">
              <el-tree
                :data="tableData"
                node-key="id"
                check-strictly
                ref="tree"
                highlight-current
                :props="defaultProps"
              ></el-tree>
            </el-option>
          </el-select>
        </el-form-item>
        <el-row>
          <el-col :span="12">
            <el-form-item
              label="部门名称"
              prop="name"
              :label-width="formLabelWidth"
            >
              <el-input v-model="dialogForm.name" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item
              label="负责人"
              prop="linkMen"
              :label-width="formLabelWidth"
            >
              <el-input
                v-model="dialogForm.linkMen"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-col :span="12">
            <el-form-item
              label="显示顺序"
              prop="order"
              :label-width="formLabelWidth"
            >
              <el-input-number
                v-model="dialogForm.order"
                @change="handleChange"
                :min="0"
                :max="10000"
                label="角色顺序"
              ></el-input-number>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="部门状态" :label-width="formLabelWidth">
              <el-radio-group v-model="dialogForm.state">
                <el-radio label="1">正常</el-radio>
                <el-radio label="0">停用</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row
          ><el-col :span="12">
            <el-form-item
              label="联系电话"
              prop="phone"
              :label-width="formLabelWidth"
            >
              <el-input
                v-model="dialogForm.phone"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item
              label="邮箱"
              prop="email"
              :label-width="formLabelWidth"
            >
              <el-input
                v-model="dialogForm.email"
                autocomplete="off"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>

        <el-form-item label="备注" :label-width="formLabelWidth">
          <el-input
            type="textarea"
            v-model="dialogForm.remark"
            autocomplete="off"
          ></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="save">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { dateFormat } from "@/utils/index";
export default {
  data() {
    return {
      statusData: "",
      statusValueData: "",
      defaultProps: {
        children: "children",
        label: "name",
      },
      dialogTitle: "添加部门",
      dialogFormVisible: false, //是否显示弹框
      dialogForm: {
        roleId: "",
        name: "",
        powerKey: "",
        order: "",
        state: "1",
        phone: "",
        email: "",
        linkMen: "",
        date: dateFormat(new Date(), "yyyy-MM-dd hh:mm:ss"),
        remark: "",
      },
      rules: {
        name: [{ required: true, message: "部门名称", trigger: "blur" }],
        powerKey: [
          { required: true, message: "请输入权限字符", trigger: "blur" },
        ],
        linkMen: [{ required: true, message: "请输入负责人", trigger: "blur" }],
        phone: [{ required: true, message: "请输入联系电话", trigger: "blur" }],
        email: [{ required: true, message: "请输入邮箱", trigger: "blur" }],
        state: [{ required: true, message: "请选择状态", trigger: "change" }],
      },
      formLabelWidth: "80px",
      valueDate: "", //日期
      formInline: {
        name: "",
        state: "",
      },
      tableData: [
        {
          id: "1",
          name: "唐山支队",
          linkMen: "",
          order: "0",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "最大权限",
          children: [
            {
              id: "11",
              name: "玉田大队",
              linkMen: "",
              order: "1",
              phone: "15632547845",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "632145478@qq.com",
              remark: "",
            },
            {
              id: "12",
              name: "唐山大队",
              linkMen: "",
              order: "2",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
          ],
        },
        {
          id: "2",
          name: "秦皇岛支队",
          linkMen: "",
          order: "2",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
          children: [
            {
              id: "21",
              name: "秦皇岛大队",
              linkMen: "",
              order: "1",
              phone: "15632547845",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "632145478@qq.com",
              remark: "",
            },
            {
              id: "22",
              name: "北戴河大队",
              linkMen: "",
              order: "2",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
          ],
        },
        {
          id: "3",
          name: "保通保畅",
          linkMen: "",
          order: "3",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
          children: [
            {
              id: "31",
              name: "保通保畅应急大队（秦皇岛）",
              linkMen: "陈东辉",
              order: "1",
              phone: "15632547845",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "632145478@qq.com",
              remark: "",
            },
            {
              id: "32",
              name: "保通保畅应急大队（唐山）",
              linkMen: "江海",
              order: "2",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "33",
              name: "保通保畅应急大队（迁安）",
              linkMen: "杜石磊",
              order: "3",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "34",
              name: "救援清障（秦皇岛）",
              linkMen: "",
              order: "4",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "35",
              name: "救援清障（迁安）",
              linkMen: "",
              order: "5",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "36",
              name: "救援清障（唐山）",
              linkMen: "",
              order: "6",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
          ],
        },
          {
          id: "4",
          name: "收费站",
          linkMen: "",
          order: "4",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
          children: [
            {
              id: "41",
              name: "玉田站",
              linkMen: "李峥",
              order: "1",
              phone: "15632547845",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "632145478@qq.com",
              remark: "",
            },
            {
              id: "42",
              name: "唐山北站",
              linkMen: "庞春峰",
              order: "2",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "43",
              name: "鸦鸿桥（主站）",
              linkMen: "安娜",
              order: "3",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "44",
              name: "榛子镇站",
              linkMen: "姚少波",
              order: "4",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "45",
              name: "迁安站（主站）",
              linkMen: "柳江",
              order: "5",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "46",
              name: "卢龙站",
              linkMen: "张杰",
              order: "6",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
             {
              id: "47",
              name: "抚宁站",
              linkMen: "何键",
              order: "7",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
             {
              id: "48",
              name: "北戴河站",
              linkMen: "王冰",
              order: "8",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },

            {
              id: "49",
              name: "秦皇岛站",
              linkMen: "杜亚军",
              order: "9",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "50",
              name: "秦皇岛北站",
              linkMen: "王玉飞",
              order: "10",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "410",
              name: "秦皇岛东站",
              linkMen: "宋银波",
              order: "11",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "411",
              name: "北戴河站",
              linkMen: "祖山站",
              order: "12",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "412",
              name: "孟姜站",
              linkMen: "张志华",
              order: "13",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
          ],
        },
          {
          id: "5",
          name: "养护",
          linkMen: "",
          order: "5",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
          children: [
            {
              id: "51",
              name: "养护巡查（唐山）",
              linkMen: "杨兆顺",
              order: "1",
              phone: "15632547845",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "632145478@qq.com",
              remark: "",
            },
            {
              id: "52",
              name: "养护巡查（迁安）",
              linkMen: "位赛厦",
              order: "2",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
            {
              id: "53",
              name: "养护巡查（秦皇岛）",
              linkMen: "赵成卫",
              order: "3",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
           
          ],
        },
          {
          id: "6",
          name: "机电",
          linkMen: "",
          order: "6",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
          children: [
            {
              id: "61",
              name: "唐山所机电",
              linkMen: "",
              order: "1",
              phone: "15632547845",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "632145478@qq.com",
              remark: "",
            },
            {
              id: "62",
              name: "秦皇岛所机电",
              linkMen: "",
              order: "2",
              phone: "13562478457",
              state: "1",
              date: "2023-7-21 15:57:36",
              email: "563247845@qq.com",
              remark: "",
            },
          ],
        },
         {
          id: "7",
          name: "路政",
          linkMen: "",
          order: "7",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
         },
         {
          id: "8",
          name: "相邻高速",
          linkMen: "",
          order: "8",
          phone: "13545124547",
          state: "1",
          date: "2023-7-21 15:57:36",
          email: "11655070124@qq.com",
          remark: "",
         }
      ],
      multipleSelection: [],
      mydata: null, //暂存数据
      myIndex: null,
    };
  },
  methods: {
    // 角色顺序事件

    handleChange(val) {},
    // 点击查询
    searchClick() {
      debugger;
    },
    // 勾选事件
    handleSelectionChange(val) {
      debugger;
      if (val.length == 1) {
        this.disabledEdit = false;
        this.disabledDeleted = false;
        this.mydata = val[0];
        this.tableData.forEach((ele, i) => {
          if (ele.powerKey == val[0].powerKey) {
            this.myIndex = i;
          }
        });
      } else {
        this.disabledEdit = true;
        this.disabledDeleted = true;
      }
      this.multipleSelection = val;
    },
    // 新增弹窗出现
    addRow() {
      this.dialogFormVisible = true;
      this.$nextTick(() => {
        this.$refs["ruleForm"].resetFields();
        this.dialogForm.state = "1";
        this.dialogForm.remark = "";
      });
    },
    // 弹窗里面的保存按钮
    save() {
      if (this.dialogTitle == "添加角色") {
        debugger;
        this.dialogForm.roleId = this.tableData.length + 1;
        this.tableData.push(JSON.parse(JSON.stringify(this.dialogForm)));
      } else {
        this.tableData.forEach((item) => {
          if (item.roleId == this.dialogForm.roleId) {
            this.$set(item, "roleId", this.dialogForm.roleId);
            this.$set(item, "name", this.dialogForm.name);
            this.$set(item, "powerKey", this.dialogForm.powerKey);
              this.$set(item, "linkMen", this.dialogForm.linkMen);
               this.$set(item, "phone", this.dialogForm.phone);
            this.$set(item, "order", this.dialogForm.order);
            this.$set(item, "state", this.dialogForm.state);
            this.$set(item, "date", this.dialogForm.date);
              this.$set(item, "email", this.dialogForm.email);
            this.$set(item, "remark", this.dialogForm.remark);
          }
        });
      }
      this.$message({
        type: "success",
        message: "操作成功!",
      });
      this.dialogFormVisible = false;
    },
    // 编辑
    editClick(row) {
      this.dialogFormVisible = true;
      this.dialogTitle = "编辑部门";
      this.$nextTick(() => {
        this.$refs["ruleForm"].resetFields();
        this.dialogForm.roleId = row.roleId;
        this.dialogForm.name = row.name;
        this.dialogForm.powerKey = row.powerKey;
          this.dialogForm.order = row.order;
        this.dialogForm.linkMen = row.linkMen;
         this.dialogForm.phone = row.phone;
        this.dialogForm.state = row.state;
         this.dialogForm.email = row.email;
        this.dialogForm.date = row.date;
        this.dialogForm.remark = row.remark;
      });
    },
    // 删除
    deleteds() {
      this.deletedClick(this.myIndex, this.mydata);
    },
    // 编辑
    edits() {
      this.editClick(this.mydata);
    },
    // 删除
    deletedClick(index, row) {
      if (row.name == "超级管理员") {
        this.$message({
          type: "warning",
          message: "该角色是超级管理员不能删除!",
        });
        return;
      }
      this.$confirm("确定要删除“" + row.name + "”该角色?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.tableData.splice(index, 1);
          this.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    // 打开关闭的状态
    switchChange(state, row) {
      if (state == 1) {
        this.$message({
          type: "success",
          message: "状态开启!",
        });
      } else {
        this.$message({
          type: "warning",
          message: "状态关闭!",
        });
      }
    },
  },
};
</script>

<style scoped>
.organization {
  margin-top: 10px;
}
.demonstration {
  margin-right: 10px;
}
.organization-table {
  margin-top: 20px;
}
.organization-table /deep/ .el-table th,
.el-table tr {
  background: #f8f8f9;
}
.edits {
  color: #1890ff;
  margin-right: 10px;
  cursor: pointer;
  font-size: 14px;
}
.deleteds {
  color: #f56c6c;
  cursor: pointer;
  font-size: 14px;
}
.el-dialog--center .el-dialog__body {
  padding: 10px;
}
.organization /deep/ .el-table th > .cell {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}
.stateTip {
  background-color: #e8f4ff;
  border-color: #d1e9ff;
  display: inline-block;
  height: 32px;
  padding: 0 10px;
  line-height: 30px;
  font-size: 12px;
  color: #1890ff;
  border-width: 1px;
  border-style: solid;
  border-radius: 4px;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  white-space: nowrap;
}
.el-select-dropdown__item.hover,
.el-select-dropdown__item:hover {
  background: #fff;
}
</style>