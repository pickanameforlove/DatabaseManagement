<template>
  <div class="app-container">
    <el-col v-loading="listLoading">
      <el-table :data="tableData" element-loading-text="Loading" border fit highlight-current-row>
        <el-table-column align="center" label="ID" width="95">
          <template slot-scope="scope">{{ scope.$index }}</template>
        </el-table-column>
        <el-table-column label="Train_no">
          <template slot-scope="scope">{{ scope.row.train_no }}</template>
        </el-table-column>
        <el-table-column label="Train_code" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.train_code }}</span>
          </template>
        </el-table-column>
        <el-table-column label="S_plat" align="center">
          <template slot-scope="scope">{{ scope.row.s_plat }}</template>
        </el-table-column>
        <el-table-column label="E_plat" align="center">
          <template slot-scope="scope">{{scope.row.e_plat}}</template>
        </el-table-column>
        <el-table-column label="操作">
          <template scope="scope">
            <el-button type="danger" size="small" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="block">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-size="pageSize"
          layout="prev, pager, next, jumper"
          :total="currentTotal"
        ></el-pagination>
      </div>
    </el-col>
  </div>
</template>

<script>
export default {
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: "success",
        draft: "gray",
        deleted: "danger"
      };
      return statusMap[status];
    }
  },
  data() {
    return {
      list: null,
      listLoading: true,
      currentPage: 1,
      pageSize: 15,
      currentTotal: 30
    };
  },
  created() {
    // this.fetchData()
    var that = this;
    console.log(
      "http://localhost:8080/TrainManageSystem_war_exploded/user/train/select/count"
    );
    this.$http
      .get(
        "http://localhost:8080/TrainManageSystem_war_exploded/user/train/select/count"
      )
      .then(function(response) {
        that.currentTotal = parseInt(response.data);
        console.log(response.data);
      });
    this.$http
      .get(
        "http://localhost:8080/TrainManageSystem_war_exploded/user/train/select/batchTrains",
      {
        params: {
          start: (that.currentPage - 1) * that.pageSize,
          end: that.pageSize
        }
      }
      )
      .then(function(response) {
        that.list = response.data;
        that.tableData = response.data;
        that.listLoading = false;
        console.log(response.data);
      });
  },
  methods: {
    handleDelete(index, row) {
      console.log(row.train_no);
      this.tableData.splice(index, 1);
      this.$http
        .get(
          "http://localhost:8080/TrainManageSystem_war_exploded/user/train/delete",
        {
          params: {
            train_no: row.train_no
          }
        }
        )
        .then(function(response) {
          // var status = response.data["status"];
          // if (status === 'true'){
          //   this.$message({
          //     message: "操作成功！",
          //     type: "success"
          //   });
          // }
        });
      this.$message({
        message: "操作成功!",
        type: "success"
      });
    },
    handleEdit(index, row) {
      // this.dialogFormVisible = true;
      // this.form = Object.assign({}, row);
      // this.table_index = index;
    },
    fetchData() {
      this.listLoading = true;
      //   getList().then(response => {
      //     this.list = response.data.items;
      //     this.listLoading = false;
      //   });
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      console.log(`当前页: ${val}`);
      var that = this;
      this.$http
        .get(
          "http://localhost:8080/TrainManageSystem_war_exploded/user/train/select/batchTrains",
        {
          params: {
            start: (that.currentPage - 1) * that.pageSize,
            end: that.pageSize
          }
        }
        )
        .then(function(response) {
          that.list = response.data;
          that.listLoading = false;
          console.log(response.data);
        });
    }
  }
};
</script>
