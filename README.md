<template>
    <div>
        <header class="tableHeader">
            <div>
                <span>日期</span>
                <el-date-picker
                    v-model="dataValue"
                    type="daterange"
                    align="right"
                    unlink-panels
                    range-separator="To"
                    value-format="yyyy-MM-dd"
                    start-placeholder="开始日期"
                    end-placeholder="结束日期"
                    :picker-options="pickerOptions"
                >
                </el-date-picker>
            </div>
            <div>
                <span>处理状态</span>
                <el-select v-model="selectValue" placeholder="请选择" clearable>
                    <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value"> </el-option>
                </el-select>
            </div>
            <div><el-button size="small" plain @click="handleFind">查询</el-button></div>
            <div><el-button size="small" @click="handleReset">重置</el-button></div>
        </header>
        <el-table :data="tableData" stripe style="width: 100%" border lazy>
            <el-table-column prop="mgCreateDate" label="日期"></el-table-column>
            <el-table-column prop="mgName" label="姓名" width="100"></el-table-column>
            <el-table-column prop="mgTel" label="联系电话"></el-table-column>
            <el-table-column prop="mgEmail" label="邮箱"></el-table-column>
            <el-table-column prop="mgContent" label="问题描述"></el-table-column>
            <el-table-column label="附件" width="100">
                <template slot-scope="scope">
                    <el-tag
                        :class="scope.row.mgHasFile === true ? 'haveFile' : ''"
                        :type="scope.row.mgHasFile === true ? '' : 'info'"
                        @click="showImg(scope.row.mgId, scope.row.mgHasFile)"
                        >{{ scope.row.mgHasFile === true ? '有' : '无' }}</el-tag
                    >
                </template>
            </el-table-column>
            <el-table-column label="处理状态" width="100">
                <template slot-scope="scope">
                    <span v-show="scope.row.mgDealStatus == 0 ? true : false">{{ scope.row.mgDealStatus == 0 ? '未处理' : '' }}</span>
                    <span v-show="scope.row.mgDealStatus == 1 ? true : false">{{ scope.row.mgDealStatus == 1 ? '处理中' : '' }}</span>
                    <span v-show="scope.row.mgDealStatus == 2 ? true : false">{{ scope.row.mgDealStatus == 2 ? '已处理' : '' }}</span>
                </template>
            </el-table-column>
            <el-table-column prop="diCreateDate" label="处理时间"></el-table-column>
            <el-table-column prop="uiNickname" label="处理人" width="120"></el-table-column>
            <el-table-column prop="operation" label="操作">
                <template slot-scope="scope">
                    <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">查看消息</el-button>
                    <el-button size="mini" type="primary" @click="handlemsg(scope.$index, scope.row)">处理</el-button>
                </template>
            </el-table-column>
        </el-table>
        <Dialog  @dialogClose = "dialogClose"  :dialogIs='dialogIs' :dialogTitle='dialogTitle' :dialogShow="Visible"></Dialog>
        <el-pagination
            @current-change="handleCurrentChange"
            :current-page="currentPage4"
            layout="total, prev, pager, next, jumper"
            :total="total"
        >
        </el-pagination>
    </div>
</template>

<script>
import { getList, getFileList } from '../../api/index';
import Header from '../common/Header.vue';
import Dialog from './Dialog.vue';

export default {
    components: { Header, Dialog },
    provide() {
        return {
            getParent: () => this.mId
        };
    },
    data() {
        return {
            tableData: [],
            pickerOptions: {
                shortcuts: [
                    {
                        text: '最近一周',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
                            picker.$emit('pick', [start, end]);
                        }
                    },
                    {
                        text: '最近一个月',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
                            picker.$emit('pick', [start, end]);
                        }
                    },
                    {
                        text: '最近三个月',
                        onClick(picker) {
                            const end = new Date();
                            const start = new Date();
                            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
                            picker.$emit('pick', [start, end]);
                        }
                    }
                ]
            },
            dataValue: '',
            options: [
                {
                    value: '0',
                    label: '未处理'
                },
                {
                    value: '1',
                    label: '处理中'
                },
                {
                    value: '2',
                    label: '已处理'
                }
            ],
            selectValue: '',
            currentPage4: 0,
            total: 0,
            data: {
                pageNum: 1,
                dealStatus: '',
                startDate: '',
                endDate: ''
            },
            Visible: false,
            urlList: [],
            dialogTitle:'',
            dialogIs:'',
            mId:''
        };
    },
    created() {
        this.getData(this.data);
    },
    mounted() {},
    methods: {
        //获取留言列表
        async getData() {
            let res = await getList();
            if (res.code === 200) {
                this.tableData = res.rows;
                this.total = res.total;
            }
        },

        dialogClose (data) {
            this.Visible = data
        },
        //查看留言
        handleEdit(index, row) {
            console.log(index, row);
        },
        //处理
        handlemsg(index, row) {
            console.log(index, row);
        },
        //查询
        handleFind() {
            this.data.startDate = this.dataValue[0];
            this.data.endDate = this.dataValue[1];
            this.data.dealStatus = this.selectValue;
            this.getData(this.data);
        },
        //当前页
        handleCurrentChange(val) {
            this.data.pageNum = val;
            this.getData(this.data);
        },
        //显示附件
        async showImg(id, data) {
            if (data === true) {
                this.Visible = true;
                this.dialogTitle = '附件';
                this.dialogIs ='LmgList'
                this.mId = id
                // let res = await getFileList(id);
                // res.imageSrcArr.forEach((item) => {
                //     this.urlList.push(this.$img + item);
                // });
                // this.getParentVal(this.urlList);
            }
        },
        //重置
        handleReset() {
            this.data = {
                pageNum: 1,
                dealStatus: '',
                startDate: '',
                endDate: ''
            };
            this.getData(this.data);
        },
        //向孙组件传值
        getParentVal(val) {
            return val;
        }
    }
};
</script>

<style scoped>
.tableHeader {
    display: flex;
    justify-content: space-around;
}
.el-table {
    margin-top: 60px;
}
.el-pagination {
    text-align: center;
    margin-top: 20px;
}
.haveFile {
    cursor: pointer;
}

.el-table .el-table__cell {
    text-align: center !important;
}
.el-table .cell {
    word-break: inherit !important;
}
</style>
