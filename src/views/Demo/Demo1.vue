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
                    <el-card class="box-card">
                        <el-row>
                            <el-col :span="24">
                                <el-table :data="result" style="width: 100%">
                                    <template slot="empty">無資料</template>
                                    <el-table-column prop="id" label="id"></el-table-column>
                                    <el-table-column prop="book_name" label="書名"></el-table-column>
                                    <el-table-column prop="book_price" label="價格"></el-table-column>
                                    <el-table-column label="操作">
                                        <el-button type="danger" size="small" @click="Delete">刪除</el-button>
                                    </el-table-column>
                                </el-table>
                            </el-col>
                        </el-row>
                    </el-card>
                </el-col>
            </el-row>
        </el-col>
    </el-row>
</template>

<script>
export default {
    created() {
        this.Get()
    },
    mounted() {},
    data() {
        return {
            // 關鍵字
            key_words: '',
            // 查詢結果
            result: [],
            // 查詢結果區塊loading
            result_loading: false
        }
    },
    methods: {
        /**
         * 查詢書本名稱
         */
        Get() {
            // 如果有關鍵字
            let api = ''
            if (this.key_words) api = `https://220.132.96.239/sunrise_demo1_bk/api/get_books/${this.key_words}`
            else api = `https://220.132.96.239/sunrise_demo1_bk/api/get_books`
            this.$http.get(api).then((res) => {
                // 賦值
                this.result = res.data.data
            })
        },
        /**
         * 新增書本名稱
         */
        Post() {},
        /**
         * 刪除書本
         */
        Delete() {
            this.$http
                .post(`https://220.132.96.239/sunrise_demo1_bk/api/delete_books/${this.key_words}`)
                .then
                // 判斷成功或失敗
                ()
        },
        /**
         * TODO 更新書本名稱
         */
        Patch() {}
    }
}
</script>
