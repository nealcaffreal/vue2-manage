<!--
 * @Author: yangming
 * @LastEditTime: 2022-09-14 14:33:01
 * @Description: 
-->
<template>
  <div class="fillcontain">
    <head-top></head-top>
    <div class="table_container">
      <div
        style="
          display: flex;
          flex-direction: row-reverse;
          align-items: center;
          height: 60px;
        "
      >
        <el-button type="primary" @click="handlerAdd"
          >新增</el-button
        >
      </div>
      <el-dialog :title="title" :visible.sync="dialogVisible" size="tiny">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="类型名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="类型描述">
            <el-input v-model="form.des"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">提交</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="name" label="类型名称"></el-table-column>
        <el-table-column prop="des" label="类型描述"> </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)"
              >编辑</el-button
            >
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <div class="Pagination" style="text-align: right; margin-top: 10px">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[10, 20, 30, 40]"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="count"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import headTop from "../components/headTop";
import { getSchoolTypeList,addSchoolType,updateSchoolType,deleteSchoolType } from "@/api/getData";
export default {
  data() {
    return {
      tableData: [],
      currentRow: null,
      offset: 0,
      limit: 10,
      count: 0,
      currentPage: 1,
      dialogVisible: false,
      form: {
        name: "",
        des: ''
      },
      type_id: 1,
      isAdd: true,
      title: '新增学校'
    };
  },
  components: {
    headTop,
  },
  created() {
    this.getSchoolTypeListHttp();
  },
  methods: {
    async addSchoolTypeHttp() {
      console.log(this.form)
      const res = await addSchoolType(this.form)
      if (res.status == 1) {
        this.dialogVisible = false;
        this.$refs['form'].resetFields();
        this.getSchoolTypeListHttp();
      } else {
        throw new Error(res.message);  
      }
    },
    async updateSchoolTypeHttp(id) {
      const res = await updateSchoolType({id,...this.form})
      if (res.status == 1) {
        this.dialogVisible = false;
        this.$refs['form'].resetFields();
        this.getSchoolTypeListHttp();
      } else {
        throw new Error(res.message);
      }
    },
    async deleteSchoolTypeHttp(id) {
      const res = await deleteSchoolType(id)
      if(res.status == 1) {
        this.getSchoolTypeListHttp();
      }
    },
    handlerAdd() {
      this.dialogVisible = true;
      this.title = '新增学校类型';
      this.isAdd = true;
      this.$nextTick(() => {
        this.form.name = ''
        this.form.des = ''
      })
    },
    handleEdit(index,row) {
      this.dialogVisible = true;
      this.title = '修改学校类型';
      this.isAdd = false;
      this.form = row;
      this.type_id = row.id;
    },
    handleDelete(index,{ id }) {
      this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteSchoolTypeHttp(id)
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
    },
    onSubmit() {
      if(this.isAdd) {
        this.addSchoolTypeHttp()
      }else {
        this.updateSchoolTypeHttp({id:this.type_id,...this.form})
      }
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.offset = (val - 1) * this.limit;
      this.getSchoolTypeListHttp();
    },
    async getSchoolTypeListHttp() {
      try {
        const res = await getSchoolTypeList({
          offset: this.offset,
          limit: this.limit,
        });
        if (res.status == 1) {
          this.tableData = res.data;
          this.count = res.total;
        } else {
          throw new Error(res.message);
        }
      } catch (err) {
        console.log("获取数据失败", err);
      }
    },
  },
};
</script>

<style lang="less">
@import "../style/mixin";

.table_container {
  padding: 20px;
}
</style>


