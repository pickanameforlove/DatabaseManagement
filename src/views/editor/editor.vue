<template>
  <section>
    <el-row>
      <el-col :span="24">
        <el-table
          :data="tableData"
          border
          style="width: 100%">
          <el-table-column
            prop="from_station"
            label="开始车站">
          </el-table-column>
          <el-table-column
            prop="to_station"
            label="结束车站">
          </el-table-column>
          <el-table-column
            prop="id"
            label="乘客身份证号">
          </el-table-column>
          <el-table-column
            label="日期">
              <template scope="scope">
                <div>{{scope.row.date}}</div>
              </template>
          </el-table-column>
          <el-table-column
            prop="carriage_id"
            label="车厢号">
          </el-table-column>
           <el-table-column
            prop="seat_number"
            label="座位号">
          </el-table-column>
          <!-- <el-table-column label="操作">
            <template scope="scope">
              <el-button type="primary" size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
              <el-button type="danger" size="small" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
            </template>
          </el-table-column> -->
        </el-table>
        <div class="block">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-size="100"
            layout="prev, pager, next, jumper"
            :total="1000">
          </el-pagination>
        </div>
      </el-col>
    </el-row>
    <el-dialog title="修改个人信息" :visible="dialogFormVisible" size="tiny">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="车票编号">
          <el-input v-model="form.ticket_id"></el-input>
        </el-form-item>
        <el-form-item label="火车编号">
          <el-input v-model="form.train_no"></el-input>
        </el-form-item>
        <el-form-item label="开始站点">
          <el-input v-model="form.from_station"></el-input>
        </el-form-item>
        <el-form-item label="结束站点">
          <el-input v-model="form.to_station"></el-input>
        </el-form-item>
        <el-form-item label="乘客身份证号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="座位类型">
          <el-input v-model="form.seat_type"></el-input>
        </el-form-item>
        <el-form-item label="价格">
          <el-input v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="日期">
          <el-input v-model="form.date"></el-input>
        </el-form-item>
        <el-form-item label="开始时间">
          <el-input v-model="form.start_time"></el-input>
        </el-form-item>
        <el-form-item label="结束时间">
          <el-input v-model="form.end_time"></el-input>
        </el-form-item>
        <el-form-item label="车厢号">
          <el-input v-model="form.carriage_id"></el-input>
        </el-form-item>
         <el-form-item label="座位号">
          <el-input v-model="form.seat_number"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleSave" :loading="editLoading">修改</el-button>
          <el-button @click="dialogFormVisible = false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </section>
</template>
<script type="text/ecmascript-6">
  const ERR_OK = "000";
  export default {
    data () {
      return {
        formInline: {
          user: {
            name: '',
            date: '',
            address: [],
            place: ''
          }
        },
        tableData: [],
        options: [],
        places: [],
        dialogFormVisible: false,
        editLoading: false,
        form: {
          ticket_id: '',
          train_no: '',
          from_station: '',
          to_station: '',
          id: '',
          seat_type: '',
          price: '',
          date: '',
          start_time: '',
          end_time: '',
          arrive_day_diff: '',
          carriage_id: '',
          seat_number: '',
          order_id: ''
        },
        currentPage: 4,
        table_index: 999,
      };
    },
    created () {
      var that = this
      this.$http
      .get(
        "http://localhost:8080/TrainManageSystem_war_exploded/user/ticket/select/all",
        )
      .then(function(response) {
        that.tableData = response.data;
        for (var i = 0; i < that.tableData.length; i++) {
          var te = new Date(that.tableData[i]['date'])
          var month = te.getMonth() + 1
          var temf = te.getFullYear() + '-' + month + '-' + te.getDate()
          that.tableData[i]['date'] = temf
        }
      });
      this.$http.get('/api/getOptions').then((response) => {
        response = response.data;
        if (response.code === ERR_OK) {
          this.options = response.datas;
          this.places = response.places;
        }
      });
    },
    methods: {
      onSubmit () {
        this.$message('模拟数据，这个方法并不管用哦~');
      },
      handleDelete (index, row) {
        // this.tableData.splice(index, 1);
        // this.$http.get("http://localhost:8080/TrainManageSystem_war_exploded/user/user/delete/user", {
        //   params: {
        //     account: row.account
        //   }
        // }).then(function(response){
        //   console.log('45')
        // })
        // this.$message({
        //   message: "操作成功！",
        //   type: 'success'
        // });
      },
      handleEdit (index, row) {
        this.dialogFormVisible = true;
        this.form = Object.assign({}, row);
        this.table_index = index;
      },
      handleSave () {
        this.$confirm('确认提交吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          cancelButtonClass: 'cancel'
        }).then(() => {
          this.editLoading = true;
          let date = this.form.date;
          if (typeof date === "object") {
            date = [date.getFullYear(), (date.getMonth() + 1), (date.getDate())].join('-');
            this.form.date = date
          }
//          this.tableData[this.table_index] = this.form;
          this.tableData.splice(this.table_index, 1, this.form);
          this.$message({
            message: "操作成功！",
            type: 'success'
          });
          this.editLoading = false;
          this.dialogFormVisible = false;
        }).catch(() => {

        });
      },
      download: function() {
        console.log("xiazai")
        var obj = document.getElementById('download');
        var str = "姓名,出生日期,地址\n";
        for (var i = 0; i < this.tableData.length; i++) {
          var item = this.tableData[i];
          str += item.name + ',' + item.date + ',' + item.address;
          str += "\n";
        }
        console.log(obj)
        str = encodeURIComponent(str);
        console.log(str)
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
