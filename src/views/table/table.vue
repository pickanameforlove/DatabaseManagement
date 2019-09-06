<template>
  <section>
    <el-row>
      <el-col :span="24">
        <el-form>
          <el-button type="primary" @click="add" style="float:right">添加用户</el-button>
        </el-form>
        <!--表单-->
        <!-- <el-form :inline="true" :model="formInline" class="demo-form-inline">
          <el-form-item label="姓名">
            <el-input v-model="formInline.user.name" placeholder="姓名" style="width: 140px;"></el-input>
          </el-form-item>
          <el-form-item label="年份">
            <el-date-picker
              v-model="formInline.user.date"
              align="right"
              type="year"
              placeholder="选择年份">
            </el-date-picker>
          </el-form-item>
          <el-form-item label="地址">
            <el-cascader expand-trigger="hover" :options="options" v-model="formInline.user.address"></el-cascader>
          </el-form-item>
          <el-form-item label="籍贯">
            <el-select v-model="formInline.user.place" placeholder="请选择">
              <el-option
                v-for="item in places"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-button type="primary" @click="onSubmit">查询</el-button>
          <a href="javascript:;" id="download" style="background-color:#409EFF;color: #fff;padding: 12px 10px!important;margin-left: 10px!important;border-radius:4px " @click="download()" download="download.csv">导出数据</a>
        </el-form>-->
        <!--表格-->
        <el-table :data="tableData" border style="width: 100%">
          <el-table-column prop="account" label="账号"></el-table-column>
          <el-table-column prop="password" label="密码"></el-table-column>
          <el-table-column prop="id" label="身份证号"></el-table-column>
          <el-table-column label="操作">
            <template scope="scope">
              <el-button type="primary" size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
              <el-button
                type="danger"
                size="small"
                @click="handleDelete(scope.$index, scope.row)"
              >删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <div class="block">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-size="100"
            layout="prev, pager, next, jumper"
            :total="1000"
          ></el-pagination>
        </div>
      </el-col>
    </el-row>
    <el-dialog title="添加用户" :visible="dialogFormVisible" size="tiny">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="用户账号">
          <el-input v-model="form.account"></el-input>
        </el-form-item>
        <el-form-item label="用户密码">
          <el-input v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSave">提交</el-button>
          <el-button @click="cancel">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </section>
</template>
<script type="text/ecmascript-6">
const ERR_OK = "000";
export default {
  data() {
    return {
      formInline: {
        user: {
          name: "",
          date: "",
          address: [],
          place: ""
        }
      },
      tableData: [],
      options: [],
      places: [],
      dialogFormVisible: false,
      editLoading: false,
      form: {
        account: "",
        password: "",
        id: "",
        name: ""
      },
      currentPage: 4,
      table_index: 999
    };
  },
  created() {
    var that = this;
    this.$http
      .get(
        "http://localhost:8080/TrainManageSystem_war_exploded/user/user/select/tem"
      )
      .then(function(response) {
        that.tableData = response.data;
      });
    this.$http.get("/api/getOptions").then(response => {
      response = response.data;
      if (response.code === ERR_OK) {
        this.options = response.datas;
        this.places = response.places;
      }
    });
  },
  methods: {
    add() {
      this.dialogFormVisible = true;
    },
    cancel() {
      this.form.account = "";
      this.form.password = "";
      this.form.id = "";
      this.form.name = "";
      this.dialogFormVisible = false;
    },
    onSubmit() {
      this.$message("模拟数据，这个方法并不管用哦~");
    },
    handleDelete(index, row) {
      this.tableData.splice(index, 1);
      this.$http
        .get(
          "http://localhost:8080/TrainManageSystem_war_exploded/user/user/delete/user",
        {
          params: {
            account: row.account
          }
        }
        )
        .then(function(response) {
          console.log("45");
        });
      this.$message({
        message: "操作成功！",
        type: "success"
      });
    },
    handleEdit(index, row) {
      this.dialogFormVisible = true;
      this.form = Object.assign({}, row);
      this.table_index = index;
    },
    handleSave() {
      var that = this;
      this.$confirm("确认提交吗？", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        cancelButtonClass: "cancel"
      }).then(
        () => {
          that.dialogFormVisible = false;
          that.$http
            .post(
              "http://localhost:8080/TrainManageSystem_war_exploded/user/user/add/person",
              that.form
            )
            .then(function(response) {
              console.log("123");
            });
        },
        () => {
          this.dialogFormVisible = false;
        }
      );
    },
    download: function() {
      console.log("xiazai");
      var obj = document.getElementById("download");
      var str = "姓名,出生日期,地址\n";
      for (var i = 0; i < this.tableData.length; i++) {
        var item = this.tableData[i];
        str += item.name + "," + item.date + "," + item.address;
        str += "\n";
      }
      console.log(obj);
      str = encodeURIComponent(str);
      console.log(str);
      obj.href = "data:text/csv;charset=utf-8,\ufeff" + str;
      obj.download = "download.csv";
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      console.log(`当前页: ${val}`);
    }
  }
};
</script>
<style>
.el-pagination {
  text-align: center;
  margin-top: 30px;
}
.el-message-box__btns .cancel {
  float: right;
  margin-left: 10px;
}
</style>
