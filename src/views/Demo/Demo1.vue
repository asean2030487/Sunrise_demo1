<template>
    <el-row>
<!--        標題-->
        <el-col :span="24">
            <h2 style="color: #008585">書本查詢</h2>
        </el-col>
<!--        查詢框-->
        <el-col :span="24">
            <el-row justify="center" type="flex">
               <el-col :span="16">
                   <el-card class="box-card">
                       <el-row>
                           <el-col :span="24" style="text-align: left">
                               <h3>請輸入關鍵字</h3>
                               <el-input v-model="key_words" placeholder="請輸入關鍵字" style="width: 60%"></el-input>
                               <el-button type="primary" size="medium" style="margin-left: .5rem" @click="Search">查詢</el-button>
                           </el-col>
                       </el-row>
                   </el-card>
               </el-col>
            </el-row>
        </el-col>
<!--        結果框-->
        <el-col :span="24">
            <el-row justify="center" type="flex">
                <el-col :span="16">
                    <el-card class="box-card">
                        <el-row v-loading="result_loading">
                            <el-col :span="24" style="text-align: left">

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

        },
        mounted: {},
        data() {
            return{
                // 關鍵字
                key_words: '',
                // 查詢結果
                result: [],
                // 查詢結果區塊loading
                result_loading:false,
            }
        },
        methods: {
            /**
             * 查詢書本名稱
             * @constructor
             */
            Search(){
                // do something here
            }
        }
    }
</script>
