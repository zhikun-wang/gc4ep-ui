<template>
    <div>
        <el-row>
            <el-col :span="12">
                <div class="grid-content bg-purple ">
                    <div class="table left-table">
                        <div class="crumbs">
                            <el-breadcrumb separator="/">
                                <el-breadcrumb-item>
                                    <i class="el-icon-menu"></i> 辖区设备</el-breadcrumb-item>
                                <el-breadcrumb-item>设备查看</el-breadcrumb-item>
                            </el-breadcrumb>
                        </div>

                        <el-table :data="devData" :row-class-name="tableRowClassName" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">

                            <el-table-column prop="name" label="设备编号" width="120">
                            </el-table-column>

                           <el-table-column prop="box_status" label="垃圾仓状态" width="120">

<template slot-scope="scope">
        <el-tag
          :type="scope.row.box_status === '正常' ? 'primary' : 'danger'"
          close-transition>{{scope.row.box_status}}</el-tag>
      </template>

              </el-table-column>

              <el-table-column prop="bag_count" label="袋子数量" width="120">

                <template slot-scope="scope">
        <el-tag
          :type="scope.row.bag_count <20 ? 'danger' : 'primary'"
          close-transition>{{scope.row.bag_count}}</el-tag>
      </template>
              </el-table-column>

                            <el-table-column prop="location" label="投放地址" :formatter="formatter">
                            </el-table-column>

                        </el-table>

                    </div>

                </div>
            </el-col>
            <el-col :span="12">
                <div class="grid-content bg-purple-light">

                    <div class="table">
                        <div class="crumbs">
                            <el-breadcrumb separator="/">
                                <el-breadcrumb-item>
                                    <i class="el-icon-menu"></i> 环卫管理</el-breadcrumb-item>
                                <el-breadcrumb-item>环卫用户</el-breadcrumb-item>
                            </el-breadcrumb>
                        </div>

                        <el-table :data="userData" stripe border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">

                            <el-table-column prop="user_name" label="用户名" sortable>
                            </el-table-column>

                            <el-table-column prop="user_phone" label="手机号" width="150">
                            </el-table-column>

                            <el-table-column prop="user_status" label="当前状态" width="120">
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
            select_cate: '',
            select_word: '',
            selectTable: {},
            dialogFormVisible: false,
            dialogQRFormVisible: false,
            del_list: [],
            is_search: false
        }
    },
    // created() {
    //     this.getDevData();
    //     this.getUserData();
    // },

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
                return 'info-row';
            } else if (index === 3) {
                return 'positive-row';
            }
            return '';
        },



        handleCurrentChange(val) {
            this.cur_page = val;
            this.getData();
        },
        getDevData() {
            let self = this;
            self.url = process.env.BASE_URL + 'admin/device/'
            self.$axios.get(self.url, { page: self.cur_page }).then((res) => {
                self.devData = res.data.data;
            })
        },
        getUserData() {
            let self = this;
            self.url = process.env.BASE_URL + 'admin/env_user/'
            self.$axios.get(self.url, { page: self.cur_page }).then((res) => {
                self.userData = res.data.data;
            })
        },
        search() {
            this.is_search = true;
        },
        formatter(row, column) {
            return row.location;
        },
        formatterDate(row, column) {
            return this.$moment(row.create_date).format("YYYY-MM-DD HH:mm:ss");
        },
        formatterStatus(row, column) {
            if (row.status == 'lock') return '可以使用';
            return '已借出';
        },
        foramtterCount(row, column) {
            return 100;
        },

        filterTag(value, row) {
            return row.tag === value;
        },
        handleEdit(index, row) {
            this.$message('编辑第' + (index + 1) + '行');
        },
        handleDelete(index, row) {
            this.$message.error('删除第' + (index + 1) + '行');
        },
        delAll() {
            const self = this,
                length = self.multipleSelection.length;
            let str = '';
            self.del_list = self.del_list.concat(self.multipleSelection);
            for (let i = 0; i < length; i++) {
                str += self.multipleSelection[i].name + ' ';
            }
            self.$message.error('删除了' + str);
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
            self.url = process.env.BASE_URL + 'admin/device/' + row.id + '/lock'
            self.$axios.get(self.url, { page: self.cur_page }).then((res) => {
                console.log(res.data.success)
                if (res.data.success) {
                    this.$message('设备归还成功');
                    this.getData();
                } else {
                    this.$message.error('设备归还失败');
                }
            })
        },
        handleClose() {
            this.dialogQRFormVisible = false
            this.getData();
        },
        handleQRClose() {
            this.getData();
        },
        commitAdd() { }

    }
}
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
</style>