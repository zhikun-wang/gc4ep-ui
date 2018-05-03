<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-menu"></i>用户信息</el-breadcrumb-item>
                <el-breadcrumb-item>用户使用历史</el-breadcrumb-item>
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
        <el-table :data="tableData" border style="width: 100%" ref="multipleTable" >

            <el-table-column prop="createTime" label="操作时间" :formatter="formatterDate" sortable width="200">
            </el-table-column>

            <el-table-column prop="operatorType" label="操作类型" width="200" :formatter="formatterStatus">
            </el-table-column>

            <el-table-column prop="deviceName" label="设备编号">
            </el-table-column>

            

        </el-table>
        <div class="pagination">
            <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
            </el-pagination>
        </div>
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
            is_search: false,
            id:'1ee5802c-935a-468e-bf9a-d29654aa8695'
        }
    },
    created() {
        this.getData();
    },
   
    methods: {
        handleCurrentChange(val) {
            this.cur_page = val;
            this.getData();
        },
        getData() {
            let self = this;
            self.url = process.env.BASE_URL + 'admin/phoneUser/'+self.$route.params.userId+'/bag'
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
            if (row.operatorType == 'borrowBag') {
                return this.$moment(row.borrowTime).format("YYYY-MM-DD HH:mm:ss");
            }else 
                return this.$moment(row.returnTime).format("YYYY-MM-DD HH:mm:ss");

        },
        formatterStatus(row, column) {
            if (row.operatorType == 'borrowBag') return '出袋';
            return '还袋';
        },

        filterTag(value, row) {
            return row.tag === value;
        },
        handleEdit(index, row) {
            this.$message('编辑第' + (index + 1) + '行');
        },
     
        getParams () {
        let userId = this.$route.params.userId
        this.id = userId
        },
        
    created() {
      this.intervalid1 = setInterval(() => {
        var sid = this.stationid
        this.getData();
      }, 2000)
    
  },

  beforeDestroy () {
      clearInterval(this.intervalid1)
      //clearInterval(this.intervalid2)
    },


    },

     watch: {
      '$route': 'getParams'
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