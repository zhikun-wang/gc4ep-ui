<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-menu"></i> 设备管理</el-breadcrumb-item>
                <el-breadcrumb-item>设备查看</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
            <!-- <el-button type="primary" icon="delete" class="handle-del mr10" @click="delAll">批量删除</el-button> -->
            <!-- <el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">
                                            <el-option key="1" label="广东省" value="广东省"></el-option>
                                            <el-option key="2" label="湖南省" value="辽宁省"></el-option>
                                        </el-select> -->
            <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
            <el-button type="primary" icon="search" @click="search">搜索</el-button>
        </div>
        <el-table :data="data" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>

            <el-table-column prop="create_date" label="投放日期" :formatter="formatterDate" sortable width="180">
            </el-table-column>

            <el-table-column prop="name" label="设备编号" width="130">
            </el-table-column>
            <el-table-column prop="device_ip" label="设备IP" width="130">
              </el-table-column>

            <el-table-column prop="status" label="设备状态" :formatter="formatterStatus">
            </el-table-column>

            <el-table-column prop="location" label="投放地址" :formatter="formatter">
            </el-table-column>

            <el-table-column label="操作" width="180">
                <template scope="scope">
                    <el-button size="small" type="primary" @click="handleShowQR(scope.$index, scope.row)">查看二维码</el-button>
                    <!-- <el-button size="small" @click="handleShow2(scope.$index, scope.row)">编辑</el-button> -->
                    <el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                    <!-- <el-button size="small" @click="handleUnlock(scope.$index, scope.row)">归还</el-button> -->
                </template>
            </el-table-column>
        </el-table>
        <div class="pagination">
            <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
            </el-pagination>
        </div>

        <el-dialog title="设备二维码" v-model="dialogQRFormVisible" :before-close="handleQRClose()">
            <el-form :model="selectTable">
                <el-form-item label="设备编号" label-width="100px">
                    <el-input v-model="selectTable.name" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="投放地址" label-width="100px">
                    <span>当前城市：{{selectTable.location}}</span>
                </el-form-item>

                <el-form-item label="设备二维码" label-width="100px">
                    <img :src="BASE_URL+'qr/encode/'+selectTable.id">
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="handleClose()">关 闭</el-button>

            </div>
        </el-dialog>
        <el-dialog title="设备" v-model="dialogFormVisible">
            <el-form :model="selectTable">
                <el-form-item label="设备编号" label-width="100px">
                    <el-input v-model="selectTable.name" auto-complete="off"></el-input>
                </el-form-item>



                <el-form-item label="详细地址" label-width="100px">
                    <span>当前城市：</span>
                </el-form-item>
                <el-form-item label="店铺介绍" label-width="100px">
                    <el-input v-model="selectTable.description"></el-input>
                </el-form-item>
                <el-form-item label="联系电话" label-width="100px">
                    <el-input v-model="selectTable.phone"></el-input>
                </el-form-item>

                <el-form-item label="商铺图片" label-width="100px">

                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogQRFormVisible = false">关 闭</el-button>
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="commitAdd">确 定</el-button>
            </div>
        </el-dialog>

    </div>
</template>

<script>

export default {
    data() {
        return {
            BASE_URL: process.env.BASE_URL,
            url: './static/vuetable.json',
            tableData: [],
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
    created() {
        this.getData();
    },
    computed: {
        data() {
            const self = this;
            return self.tableData.filter(function(d) {
                let is_del = false;
                for (let i = 0; i < self.del_list.length; i++) {
                    if (d.name === self.del_list[i].name) {
                        is_del = true;
                        break;
                    }
                }
                if (!is_del) {
                    if (d.location.indexOf(self.select_cate) > -1 &&
                        (d.name.indexOf(self.select_word) > -1 ||
                            d.location.indexOf(self.select_word) > -1)
                    ) {
                        return d;
                    }
                }
            })
        }
    },
    methods: {
        handleCurrentChange(val) {
            this.cur_page = val;
            this.getData();
        },
        getData() {
            let self = this;
            self.url = process.env.BASE_URL + 'admin/device/'
            self.$axios.get(self.url, { page: self.cur_page }).then((res) => {
                self.tableData = res.data.data;
            })
        },
        search() {
            this.is_search = true;
        },
        formatter(row, column) {
            return row.location;
        },
        formatterDate(row, column) {
            console.log(row)
            return this.$moment(row.create_time).format("YYYY-MM-DD HH:mm:ss");
        },
        formatterStatus(row, column) {
            if (row.status == 'lock') return '<font style="color:red;">可以使用</font>';
            return '已借出';
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
            self.url = process.env.BASE_URL + 'admin/devices/' + row.id + '/lock'
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
        handleQRClose(){
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
</style>