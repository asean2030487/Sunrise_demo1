<template>
    <el-row>
        <!--        標題-->
        <el-col :span="24">
            <h2 style="color: #008585">書本查詢</h2>
        </el-col>
        <!--        查詢框-->
        <el-col :span="24">
            <el-row justify="center" type="flex">
                <el-col :span="16" :xl="16" :lg="18" :md="20" :sm="24" :xs="24">
                    <el-card class="box-card">
                        <el-row>
                            <el-col :span="24" style="text-align: left">
                                <h3>請輸入關鍵字</h3>
                                <el-input v-model="key_words" placeholder="請輸入關鍵字" style="width: 60%"></el-input>
                                <el-button type="primary" size="medium" style="margin-left: 0.5rem" @click="Get"
                                    >查詢</el-button
                                >
                                <!-- TODO 改"新增"按鈕的顏色 -->
                                <el-button
                                    type="primary"
                                    size="medium"
                                    style="margin-left: 0.5rem"
                                    @click="post_dialog = true"
                                    >新增</el-button
                                >
                            </el-col>
                        </el-row>
                    </el-card>
                </el-col>
            </el-row>
        </el-col>
        <!--        結果框-->
        <el-col :span="24">
            <el-row justify="center" type="flex">
                <el-col :span="16" :xl="16" :lg="18" :md="20" :sm="24" :xs="24">
                    <!-- TODO 加入Loading效果 -->
                    <el-card class="box-card">
                        <el-row>
                            <el-col :span="24">
                                <!-- TODO 表格樣式修改 -->
                                <el-table :data="result" style="width: 100%">
                                    <template slot="empty">無資料</template>
                                    <el-table-column prop="id" label="id"></el-table-column>
                                    <el-table-column prop="book_name" label="書名"></el-table-column>
                                    <el-table-column prop="book_price" label="價格"></el-table-column>
                                    <el-table-column label="操作">
                                        <template slot-scope="scope">
                                            <el-button
                                                type="success"
                                                size="small"
                                                @click=";[(patch_dialog = true), (index = scope.$index)]"
                                                >編輯</el-button
                                            >
                                            <el-button type="danger" size="small" @click="Delete(scope.row.id)"
                                                >刪除</el-button
                                            >
                                        </template>
                                    </el-table-column>
                                </el-table>
                            </el-col>
                        </el-row>
                    </el-card>
                </el-col>
            </el-row>
        </el-col>
        <!-- 新增書本 dialog -->
        <el-dialog title="提示(新增)" :visible.sync="post_dialog" width="30%">
            <el-row style="text-align:left">
                <el-col :span="24">
                    <h3>書名:</h3>
                    <el-input v-model="post_data.book_name" placeholder="請輸入書名"></el-input>
                </el-col>
                <el-col :span="24">
                    <h3>書本價格:</h3>
                    <!-- TODO 修改輸入框限制-1(價格只能輸入數字) -->
                    <el-input v-model="post_data.book_price" placeholder="請輸入書本價格"></el-input>
                </el-col>
            </el-row>
            <span slot="footer" class="dialog-footer">
                <el-button @click="post_dialog = false">取消</el-button>
                <el-button type="primary" @click="Post">確定</el-button>
            </span>
        </el-dialog>
        <!-- 編輯書本 dialog -->
        <el-dialog title="提示(編輯)" :visible.sync="patch_dialog" width="30%" v-if="result.length > 0">
            <el-row style="text-align:left">
                <el-col :span="24">
                    <h3>書名:</h3>
                    <el-input v-model="result[index].book_name" placeholder="請輸入書名"></el-input>
                </el-col>
                <el-col :span="24">
                    <h3>書本價格:</h3>
                    <!-- TODO 修改輸入框限制-2(價格只能輸入數字) -->
                    <el-input v-model="result[index].book_price" placeholder="請輸入書本價格"></el-input>
                </el-col>
            </el-row>
            <span slot="footer" class="dialog-footer">
                <el-button @click="patch_dialog = false">取消</el-button>
                <el-button type="primary" @click="Patch">確定</el-button>
            </span>
        </el-dialog>
    </el-row>
</template>

<script>
export default {
    created() {
        this.Get()
        console.log(this.post_data.book_name)
    },
    mounted() {},
    data() {
        return {
            // 關鍵字
            key_words: '',
            // 查詢結果
            result: [],
            // 查詢結果區塊loading
            result_loading: false,
            // 使用者點選"編輯"按鈕的時候，會帶入index，以確保dialog帶入正確的資料
            index: 0,
            // 新增的dialog組件開關
            post_dialog: false,
            // 使用者輸入的新增資料
            post_data: {
                book_name: '',
                book_price: ''
            },
            // 編輯的dialog組件開關
            patch_dialog: false
        }
    },
    methods: {
        /**
         * 查詢書本名稱
         */
        Get() {
            // TODO 查詢需要 啟動/關閉 loading效果
            // 如果有關鍵字
            let api = ''
            if (this.key_words) api = `http://211.72.231.157/Sunrise_demo1_bk/api/get_books/${this.key_words}`
            else api = `http://211.72.231.157/Sunrise_demo1_bk/api/get_books`
            this.$http.get(api).then((res) => {
                // 賦值
                this.result = res.data.data
            })
        },
        /**
         * 新增書本名稱
         */
        Post() {
            this.$http.post(`http://211.72.231.157/Sunrise_demo1_bk/api/post_books`, this.post_data).then((res) => {
                // 判斷成功或失敗
                if (res.data.code) {
                    alert('新增成功!')
                    // 關掉新增的dialog
                    this.post_data = false
                    // 重新查詢一次(用於更新資料)
                    this.Get()
                } else {
                    alert('新增失敗!')
                }
            })
        },
        /**
         * 刪除書本
         */
        Delete(id = '') {
            this.$http.get(`http://211.72.231.157/Sunrise_demo1_bk/api/delete_books/${id}`).then((res) => {
                // 判斷成功或失敗
                if (res.data.code) {
                    alert('刪除成功!')
                    // 重新查詢一次(用於更新資料)
                    this.Get()
                } else {
                    alert('刪除失敗!')
                }
            })
        },
        /**
         * TODO 更新書本名稱
         */
        Patch() {
            this.$http
                .post(`http://211.72.231.157/Sunrise_demo1_bk/api/patch_books`, this.result[this.index])
                .then((res) => {
                    // 判斷成功或失敗
                    if (res.data.code) {
                        alert('編輯成功!')
                        // 關掉編輯的dialog
                        this.patch_dialog = false
                        // 重新查詢一次(用於更新資料)
                        this.Get()
                    } else {
                        alert('編輯失敗!')
                    }
                })
        }
    }
}
</script>
