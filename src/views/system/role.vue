<template>
  <div class="role">
    <div>
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="角色名称">
          <el-input
            v-model="formInline.name"
            placeholder="请输入角色名称"
          ></el-input>
        </el-form-item>
        <el-form-item label="权限字符">
          <el-input
            v-model="formInline.powerKey"
            placeholder="请输入权限字符"
          ></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="formInline.state" placeholder="角色状态">
            <el-option label="正常" value="1"></el-option>
            <el-option label="停用" value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="创建时间">
          <el-date-picker
            v-model="valueDate"
            type="daterange"
            range-separator="-"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
          >
          </el-date-picker>
        </el-form-item>
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
          <el-button
            size="small"
            @click="edits"
            :disabled="disabledEdit"
            type="success"
            icon="el-icon-edit"
            >编辑</el-button
          >
          <el-button
            size="small"
            @click="deleteds"
            :disabled="disabledDeleted"
            icon="el-icon-delete"
            type="danger"
            >删除</el-button
          >
          <el-button size="small" type="warning" icon="el-icon-download"
            >导出</el-button
          >
        </el-row>
      </div>
    </div>
    <div class="role-table">
      <el-table
        ref="multipleTable"
        :data="tableData"
        tooltip-effect="dark"
        style="width: 100%"
        height="calc(100vh - 260px)"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="55"> </el-table-column>
        <el-table-column prop="roleId" label="角色编号" width="120">
        </el-table-column>

        <el-table-column prop="name" label="角色名称"> </el-table-column>
        <el-table-column prop="powerKey" label="权限字符"> </el-table-column>
        <el-table-column prop="order" label="显示顺序"> </el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-switch
              v-model="scope.row.state"
              active-value="1"
              inactive-value="0"
              @change="switchChange($event, scope.row)"
            >
            </el-switch>
          </template>
        </el-table-column>
        <el-table-column prop="date" label="创建日期" show-overflow-tooltip>
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
    <el-dialog :title="dialogTitle" width="550px" :visible.sync="dialogFormVisible">
      <el-form ref="ruleForm" :model="dialogForm" :rules="rules">
        <el-form-item
          label="角色名称"
          prop="name"
          :label-width="formLabelWidth"
        >
          <el-input v-model="dialogForm.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item
          label="权限字符"
          prop="powerKey"
          :label-width="formLabelWidth"
        >
          <el-input v-model="dialogForm.powerKey" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item
          label="角色顺序"
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
        <el-form-item label="状态" :label-width="formLabelWidth">
          <el-radio-group v-model="dialogForm.state">
            <el-radio label="1">正常</el-radio>
            <el-radio label="0">停用</el-radio>
          </el-radio-group>
        </el-form-item>
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
      disabledEdit: true, //禁止编辑按钮
      disabledDeleted: true, //禁止删除按钮
      dialogTitle: "添加角色",
      dialogFormVisible: false, //是否显示弹框
      dialogForm: {
        roleId: "",
        name: "",
        powerKey: "",
        order: "",
        state: "1",
        date: dateFormat(new Date(), "yyyy-MM-dd hh:mm:ss"),
        remark: "",
      },
      rules: {
        name: [{ required: true, message: "角色名称", trigger: "blur" }],
        powerKey: [
          { required: true, message: "请输入权限字符", trigger: "blur" },
        ],
        order: [{ required: true, message: "请输入权限字符", trigger: "blur" }],
        state: [{ required: true, message: "请选择状态", trigger: "change" }],
      },
      formLabelWidth: "80px",
      valueDate: "", //日期
      formInline: {
        name: "",
        powerKey: "",
        state: "",
      },
      tableData: [
        {
          roleId: "1",
          name: "陈东辉",
          powerKey: "admin",
          order: "1",
          state: "1",
          date: "2023-7-21 15:57:36",
          remark: "最大权限",
        },
        {
          roleId: "2",
          name: "江海",
          powerKey: "common",
          order: "2",
          state: "1",
          date: "2023-7-21 15:55:36",
          remark: "",
        },
        {
          roleId: "3",
          name: "杜石磊",
          powerKey: "manage",
          order: "3",
          state: "1",
          date: "2023-7-20 12:52:30",
          remark: "",
        },
        {
          roleId: "4",
          name: "李峥",
          powerKey: "text",
          order: "4",
          state: "1",
          date: "2023-7-22 15:52:36",
          remark: "",
        },
        {
          roleId: "5",
          name: "安娜",
          powerKey: "develop",
          order: "5",
          state: "1",
          date: "2023-7-21 15:20:36",
          remark: "",
        },
        {
          roleId: "6",
          name: "庞春峰",
          powerKey: "common",
          order: "6",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "7",
          name: "姚少波",
          powerKey: "common",
          order: "7",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "8",
          name: "柳江",
          powerKey: "common",
          order: "8",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "9",
          name: "张杰",
          powerKey: "common",
          order: "9",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "10",
          name: "何键",
          powerKey: "common",
          order: "10",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "11",
          name: "王冰",
          powerKey: "common",
          order: "11",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "12",
          name: "杜亚军",
          powerKey: "common",
          order: "12",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "13",
          name: "王玉飞",
          powerKey: "common",
          order: "13",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },

         {
          roleId: "14",
          name: "宋银波",
          powerKey: "common",
          order: "14",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
         {
          roleId: "15",
          name: "段玉桥",
          powerKey: "common",
          order: "15",
          state: "1",
          date: "2023-7-23 15:42:36",
          remark: "",
        },
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
      this.dialogTitle = "添加角色";
      this.$nextTick(() => {
        this.$refs["ruleForm"].resetFields();
        this.dialogForm.state = "1";
        this.dialogForm.remark = "";
      });
    },
    // 弹窗里面的保存按钮
    save() {
      this.$refs["ruleForm"].validate((valid) => {
        if (valid) {
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
                this.$set(item, "order", this.dialogForm.order);
                this.$set(item, "state", this.dialogForm.state);
                this.$set(item, "date", this.dialogForm.date);
                this.$set(item, "remark", this.dialogForm.remark);
              }
            });
          }
          this.$message({
            type: "success",
            message: "操作成功!",
          });
          this.dialogFormVisible = false;
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    // 编辑
    editClick(row) {
      this.dialogFormVisible = true;
      this.dialogTitle = "编辑角色";
      this.$nextTick(() => {
        this.$refs["ruleForm"].resetFields();
        this.dialogForm.roleId = row.roleId;
        this.dialogForm.name = row.name;
        this.dialogForm.powerKey = row.powerKey;
        this.dialogForm.order = row.order;
        this.dialogForm.state = row.state;
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
.role {
  margin-top: 10px;
}
.demonstration {
  margin-right: 10px;
}
.role-table {
  margin-top: 20px;
}
.role-table /deep/ .el-table th,
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
.role /deep/ .el-table th > .cell {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}
</style>