<template>
    <!-- 前端部分 -->
    <el-row type="flex" justify="center">
        <el-col :span="14">
            <el-card class="box-card" v-loading="loading">
                <!-- 標題 -->
                <div slot="header" class="clearfix">
                    <span>
                        <el-divider>
                            <h2>問與答</h2>
                        </el-divider>
                    </span>
                </div>
                <!-- 查詢框 -->
                <div class="group">
                    <span class="group_title">關鍵字</span>
                    <el-input
                        class="group_input"
                        v-model="search.keyword"
                        placeholder="請輸入關鍵字"
                        style="display:inline"
                    ></el-input>
                    <el-button type="info" class="gourp_button" @click="insertDialogVisible = true">新增</el-button>
                    <el-button type="primary" class="gourp_button" @click="Get_Question_List">查詢</el-button>
                </div>
                <!-- 結果區塊 -->
                <el-row style="text-align:left">
                    <el-col :span="12">
                        <h3>問題</h3>
                    </el-col>
                    <el-col :span="8">
                        <h3>發布時間</h3>
                    </el-col>
                    <el-col :span="4">
                        <h3>操作</h3>
                    </el-col>
                </el-row>
                <el-row v-for="(d, key) in data" :key="key" style="text-align:left;margin:10px 0">
                    <el-col :span="12">
                        {{ d.question }}
                    </el-col>
                    <el-col :span="8">
                        {{ d.cDate }}
                    </el-col>
                    <el-col :span="4">
                        <el-button
                            type="success"
                            size="small"
                            @click=";[Get_Answer(d.q_id), (editDialogVisible = true)]"
                            >編輯</el-button
                        >
                        <el-button type="danger" size="small" @click="Delete_Hard(d.q_id)">刪除</el-button>
                    </el-col>
                </el-row>
            </el-card>
        </el-col>
        <!-- 新增書本 dialog -->
        <el-dialog title="提示(新增)" :visible.sync="insertDialogVisible" width="30%">
            <el-row style="text-align:left">
                <el-col :span="24">
                    <h3>問題:</h3>
                    <el-input v-model="insert.question" placeholder="請輸入問題"></el-input>
                </el-col>
                <el-col :span="24">
                    <h3>類型:</h3>
                    <el-select v-model="detail.type" placeholder="請選擇類型" style="width:100%">
                        <el-option v-for="(item, index) in type" :key="index" :label="item.type" :value="item.type">
                        </el-option>
                    </el-select>
                </el-col>
                <el-col :span="24">
                    <h3>答案:</h3>
                    <el-input v-model="insert.answer" placeholder="請輸入答案"></el-input>
                </el-col>
                <el-col :span="24">
                    <h3>作者:</h3>
                    <el-input v-model="insert.auth" placeholder="請輸入作者"></el-input>
                </el-col>
            </el-row>
            <span slot="footer" class="dialog-footer">
                <el-button @click="insertDialogVisible = false">取消</el-button>
                <el-button type="primary" @click="Insert_QA">確定</el-button>
            </span>
        </el-dialog>
        <!-- 編輯書本 dialog -->
        <el-dialog title="提示(編輯)" :visible.sync="editDialogVisible" width="30%">
            <el-row style="text-align:left">
                <el-col :span="24">
                    <h3>問題:</h3>
                    <el-input v-model="detail.question" placeholder="請輸入問題"></el-input>
                </el-col>
                <el-col :span="24">
                    <h3>類型:</h3>
                    <el-select v-model="detail.type" placeholder="請選擇類型" style="width:100%">
                        <el-option v-for="(item, index) in type" :key="index" :label="item.type" :value="item.type">
                        </el-option>
                    </el-select>
                </el-col>
                <el-col :span="24">
                    <h3>答案:</h3>
                    <el-input v-model="detail.answer" placeholder="請輸入答案"></el-input>
                </el-col>
                <el-col :span="24">
                    <h3>作者:</h3>
                    <el-input v-model="detail.auth" placeholder="請輸入作者"></el-input>
                </el-col>
            </el-row>
            <span slot="footer" class="dialog-footer">
                <el-button @click="editDialogVisible = false">取消</el-button>
                <el-button type="primary" @click="Update_QA">確定</el-button>
            </span>
        </el-dialog>
    </el-row>
</template>

<script>
// vue 程式碼部分
export default {
    // 變數區塊
    data() {
        return {
            // 類型清單
            type: [],
            // Server網址
            api: 'http://211.72.231.157/Sunrise_demo1_bk/api/',
            // loading變數
            loading: false,
            // 搜尋的物件變數
            search: {
                keyword: ''
            },
            // 搜尋的結果資料
            data: [],
            // 詳細資料
            detail: {
                question: '',
                type: '',
                answer: '',
                auth: ''
            },
            // 新增資料
            insert: {
                question: '',
                type: '',
                answer: '',
                auth: ''
            },
            // 編輯dialog
            editDialogVisible: false,
            // 新增dialog
            insertDialogVisible: false
        }
    },
    // 功能區塊
    methods: {
        // 查詢結果
        Get_Question_List() {
            // 啟動loading
            this.loading = true
            let Get_API = ''
            if (this.search.keyword) Get_API = `${this.api}Get_Question_List/${this.search.keyword}`
            else Get_API = `${this.api}Get_Question_List`
            // 呼叫API
            this.axios.get(Get_API).then((res) => {
                console.log('查詢結果:　', res)
                // 賦值
                this.data = res.data.data
                // 關閉loading
                this.loading = false
            })
        },
        /**
         * 取得答案
         * @param {String} id => 資料的id
         */
        Get_Answer(id = '') {
            // 啟動loading
            this.loading = true
            // 呼叫API
            this.axios.get(`${this.api}Get_Answer/${id}`).then((res) => {
                console.log('查詢結果:　', res)
                // 賦值
                this.detail = res.data.data[0]
                // 關閉loading
                this.loading = false
            })
        },
        /**
         * 新增問答
         */
        Insert_QA() {
            // 啟動loading
            this.loading = true
            // 呼叫API
            this.axios.post(`${this.api}Insert_QA`, this.insert).then((res) => {
                console.log('結果:　', res)
                // 賦值
                this.detail = res.data.data
                // 關閉loading
                this.loading = false
                this.insertDialogVisible = false
                // 查詢
                this.Get_Question_List()
            })
        },
        /**
         * 編輯問答
         */
        Update_QA() {
            // 啟動loading
            this.loading = true
            // 呼叫API
            this.axios.patch(`${this.api}Update_QA`, this.detail).then((res) => {
                console.log('結果:　', res)
                // 關閉loading
                this.loading = false
                this.editDialogVisible = false
                // 查詢
                this.Get_Question_List()
            })
        },
        /**
         * 刪除問答
         */
        Delete_Hard(id = '') {
            this.$confirm('確定要刪除此筆資料嗎', '提示', {
                confirmButtonText: '確定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                // 啟動loading
                this.loading = true
                // 呼叫API
                this.axios.delete(`${this.api}Delete_Hard/${id}`).then((res) => {
                    console.log('刪除:　', res)
                    // 關閉loading
                    this.loading = false
                    this.editDialogVisible = false
                    // 查詢
                    this.Get_Question_List()
                })
            })
        },
        // 取得類型清單 (採用async非同步)
        async Get_Type_List() {
            // 呼叫API
            await this.axios.get(`${this.api}Get_Type_List`).then((res) => {
                console.log('類別查詢結果:　', res)
                // 賦值
                this.type = res.data.data
            })
        }
    },
    // mounted hook
    async mounted() {
        // 取得類別
        await this.Get_Type_List()
        console.log('類別清單載入結束!')
    }
}
</script>
<!-- css部分-->
<style scoped lang="scss">
.group {
    display: flex;
    align-items: center;
    > .group_input {
        padding: 0 30px;
    }
    > .group_title {
        width: 80px;
    }
}
</style>
