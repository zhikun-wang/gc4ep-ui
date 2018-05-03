<template>
  <div>
    <el-row>
      <el-col :span="9">
        <div class="grid-content bg-purple ">
          <div class="table left-table">
            <div class="crumbs">
              <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                  <i class="el-icon-menu"></i> 设备监控</el-breadcrumb-item>
                <el-breadcrumb-item>设备查看</el-breadcrumb-item>
              </el-breadcrumb>
            </div>

            <el-table :data="devData" stripe :row-class-name="tableRowClassName" border style="width: 100%" >

              <el-table-column prop="name" label="设备编号" width="130">
              </el-table-column>

              <el-table-column prop="device_ip" label="设备IP" width="130">
              </el-table-column>

              <el-table-column prop="box_status" label="仓状态">

<template slot-scope="scope">
        <el-tag
          :type="scope.row.box_status === '正常' ? 'primary' : 'danger'"
          close-transition>{{scope.row.box_status}}</el-tag>
      </template>

              </el-table-column>

              <el-table-column prop="bag_count" label="袋子数量">

                <template slot-scope="scope">
        <el-tag
          :type="scope.row.bag_count <20 ? 'danger' : 'primary'"
          close-transition>{{scope.row.bag_count}}</el-tag>
      </template>
              </el-table-column>

              <!-- <el-table-column prop="location" label="投放地址" >
              </el-table-column> -->

            </el-table>

          </div>

        </div>
      </el-col>
      <el-col :span="15">
        <div class="grid-content bg-purple-light">

          <div class="table">
            <div class="crumbs">
              <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                  <i class="el-icon-menu"></i> 用户管理</el-breadcrumb-item>
                <el-breadcrumb-item>用户查看</el-breadcrumb-item>
              </el-breadcrumb>
            </div>

            <el-table :data="userData" stripe border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">

              <el-table-column prop="user_name" label="用户名">
              </el-table-column>

              <el-table-column prop="user_phone" label="手机号" width="130">
              </el-table-column>

              <el-table-column prop="current_bag_count" label="当前借袋数量" width="130">
              </el-table-column>

              <el-table-column prop="borrow_bag_count" label="取袋数量" width="120">
              </el-table-column>

              <el-table-column prop="completed_bag_count" label="还袋数量" width="120">
                <template slot-scope="scope">
                  <el-button @click="userOperatorHis(scope.$index, scope.row)" type="text" size="small">{{scope.row.completed_bag_count}}</el-button>
                </template>
              </el-table-column>

              <el-table-column prop="point_sum" label="总积分" >
              </el-table-column>

            </el-table>

          </div>

        </div>
      </el-col>
    </el-row>

  </div>
</template>

<script>
export default {
  data() {
    return {
      BASE_URL: process.env.BASE_URL,
      devData: [],
      userData: [],
      cur_page: 1,
      multipleSelection: [],
      select_cate: "",
      select_word: "",
      selectTable: {},
      dialogFormVisible: false,
      dialogQRFormVisible: false,
      del_list: [],
      is_search: false
    };
  },
  created() {
      this.intervalid1 = setInterval(() => {
        var sid = this.stationid
        this.getDevData();
        this.getUserData();
      }, 2000)
    
  },

  beforeDestroy () {
      clearInterval(this.intervalid1)
      //clearInterval(this.intervalid2)
    },

  methods: {
    tableRowClassName(row, index) {
      if (index === 1) {
        return "alert-row";
      } else if (index === 3) {
        return "positive-row";
      }
      return "";
    },
    getDevData() {
      let self = this;
      self.url = process.env.BASE_URL + "admin/device/";
      self.$axios.get(self.url, { page: self.cur_page }).then(res => {
        self.devData = res.data.data;
      });
    },
    getUserData() {
      let self = this;
      self.url = process.env.BASE_URL + "admin/phone_user/";
      self.$axios.get(self.url, { page: self.cur_page }).then(res => {
        self.userData = res.data.data;
      });
    },

    foramtterCount(row, column) {
      return 100;
    },

    filterTag(value, row) {
      return row.tag === value;
    },
    handleEdit(index, row) {
      this.$message("编辑第" + (index + 1) + "行");
    },
    handleDelete(index, row) {
      this.$message.error("删除第" + (index + 1) + "行");
    },
    delAll() {
      const self = this,
        length = self.multipleSelection.length;
      let str = "";
      self.del_list = self.del_list.concat(self.multipleSelection);
      for (let i = 0; i < length; i++) {
        str += self.multipleSelection[i].name + " ";
      }
      self.$message.error("删除了" + str);
      self.multipleSelection = [];
    },
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },

    handleShow2(index, row) {
      this.selectTable = row;
      this.dialogFormVisible = true;
      //this.$message(row.name);
    },
    handleShowQR(index, row) {
      this.selectTable = row;
      this.dialogQRFormVisible = true;
    },
    handleUnlock(index, row) {
      let self = this;
      self.url = process.env.BASE_URL + "admin/device/" + row.id + "/lock";
      self.$axios.get(self.url, { page: self.cur_page }).then(res => {
        if (res.data.success) {
          this.$message("设备归还成功");
          this.getData();
        } else {
          this.$message.error("设备归还失败");
        }
      });
    },
    handleClose() {
      this.dialogQRFormVisible = false;
      this.getData();
    },
    handleQRClose() {
      this.getData();
    },
    commitAdd() {},
    userOperatorHis(index,row){
        const self = this;

        self.$router.push({
          name: 'userBagHis', 
          params: { 
              userId: row.id 
          }          
          });
    },

    
   
  }
};
</script>

<style scoped>
.handle-box {
  margin-bottom: 20px;
}

.handle-select {
  width: 120px;
}

.handle-input {
  width: 300px;
  display: inline-block;
}

.table {
  margin-top: 10px;
  margin-right: 10px;
  margin-left: 10px;
}

.el-table .info-row {
  background: #c9e5f5;
}

.el-table .positive-row {
  background: #e2f0e4;
}
.el-tag--alert {
  color: red;
}
</style>