<template>
    <div>
        <!-- <el-button @click="getAll">获取数据</el-button> -->
<!-- 新增 -->
        <el-button type="text" @click="outerVisible = true"  class="el-icon-circle-plus"></el-button>
  
        <el-dialog title="Add" :visible.sync="outerVisible">
            <el-dialog
            width="30%"
           title="提示"
            :visible.sync="innerVisible"
            append-to-body> 
                <div align="center">
                    <p class="el-icon-message-solid"></p>
                    新增数据成功
                </div>  
            </el-dialog>

            <el-form ref="form" :model="form" label-width="80px">
                <el-form-item label="书名">
                    <el-input type="text" v-model="form.name"></el-input>
                </el-form-item>

                <el-form-item label="价格">
                    <el-input type="number"  v-model="form.price"></el-input>
                </el-form-item>

                <el-form-item label="数量">
                    <el-input type="number"  v-model="form.count"></el-input>
                </el-form-item>

                <el-form-item label="出版社">
                    <el-input type="text"  v-model="form.author"></el-input>
                </el-form-item>
            </el-form>

            <div slot="footer" class="dialog-footer">
            <el-button @click="outerVisible = false">取 消</el-button>
            <el-button type="primary" @click="innerVisible = true,outerVisible=false,submit()">确定提交</el-button>
            </div>
        </el-dialog>

<!-- 表格 -->
        <el-table v-bind:data="books">
            <el-table-column prop="id" label="序号"></el-table-column>
            <el-table-column prop="name" label="书名"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="bookcount" label="数量"></el-table-column>
            <el-table-column prop="author" label="作者"></el-table-column>

            <el-table-column
            fixed="right"
            label="操作"
            width="100">
            <template slot-scope="scope">
                <el-button @click="deletebtn(scope.row)" type="text" size="small" class="el-icon-delete"></el-button>
            </template>
            </el-table-column>
        </el-table>
<!-- 分页 -->
        <el-pagination
            :total="queryDto.total"
            :page-size="queryDto.size"
            :current-page="queryDto.page"
            layout="prev,pager,next,sizes,->,total"
            :page-sizes="[5,10,15,20]"
            @current-change="currentchange"
            @size-change="sizechange"
        ></el-pagination>

    </div>
</template>

<script>
import axios from 'axios'

let app={
    data(){
        return {
            books:[],
            queryDto:{
                page:1,
                size:5,
                total:20
            },
            outerVisible:false,
            innerVisible:false,
            form:{
                name:"",
                price:0,
                count:0,
                author:""
            }


        }
    },
    mounted(){
        // const res=await axios.get('/api/books')
        // this.books=res.data.data
        // this.getAll();
        this.query();

    },
    methods:{
        async getAll(){
            const res=await axios.get("/api/books")
            this.books=res.data.data
            console.log(res);
        },

        currentchange(page){
            this.queryDto.page=page;
            this.query();
        },
        sizechange(size){
            this.queryDto.size=size;
            this.query();
        },
       async query(){
            const resp=await axios.get('/api/books/page',{
                params:{
                    toPage:this.queryDto.page,
                    pageSize:this.queryDto.size
                }
            });
            console.log(resp)
            this.queryDto.total=resp.data.data.total
            this.books=resp.data.data.records
        },
        deletebtn(row){
            axios.delete('/api/books/'+row.id)
            this.query();
        },
        submit(){
            axios.post('/api/books',{
                name:this.form.name,
                price:this.form.price,
                bookcount:this.form.count,
                author:this.form.author
            })
        }
        

    }
}
export default app

</script>


<style scoped>

</style>
