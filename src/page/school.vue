<!--
 * @Author: yangming
 * @LastEditTime: 2022-09-13 22:02:17
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
        <el-button size="mini" type="success" @click="dialogVisible = true"
          >添加学校</el-button
        >
      </div>
      <el-dialog title="提示" :visible.sync="dialogVisible" size="tiny">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="班级名称">
            <el-input v-model="form.school_name"></el-input>
          </el-form-item>
          <el-form-item label="班级描述">
            <el-input v-model="form.school_des"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">提交</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="school_name" label="班级"> </el-table-column>
        <el-table-column prop="school_des" label="描述"></el-table-column>
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
      <div class="Pagination" style="text-align: left; margin-top: 10px">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-size="20"
          layout="total, prev, pager, next"
          :total="count"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import headTop from "../components/headTop";
import { getSchoolList,addSchool,deleteSchool } from "@/api/getData";
export default {
  data() {
    return {
      tableData: [],
      currentRow: null,
      offset: 0,
      limit: 20,
      count: 0,
      currentPage: 1,
      dialogVisible: false,
      form: {
        school_name: "",
        school_des: ''
      },
    };
  },
  components: {
    headTop,
  },
  created() {
    this.getSchool();
  },
  methods: {
    async addSchoolHttp() {
      const res = await addSchool(this.form)
      if (res.status == 1) {
        this.dialogVisible = false;
        this.$refs['form'].resetFields();
        this.getSchool();
      } else {
        throw new Error(res.message);
      }
    },
    async deleteSchoolHttp(id) {
      const res = await deleteSchool(id)
      if(res.status == 1) {
        this.getSchool();
      }
    },
    handleEdit() {},
    handleDelete(index,{ id }) {
      this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.deleteSchoolHttp(id)
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
      this.addSchoolHttp()
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.offset = (val - 1) * this.limit;
      this.getSchool();
    },
    async getSchool() {
      try {
        const res = await getSchoolList({
          offset: this.offset,
          limit: this.limit,
        });
        if (res.status == 1) {
          this.tableData = res.data;
          this.total = res.total;
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


