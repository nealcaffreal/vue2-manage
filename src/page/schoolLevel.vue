<!--
 * @Author: yangming
 * @LastEditTime: 2022-09-14 22:02:18
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
          <el-form-item label="层级名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="层次描述">
            <el-input v-model="form.des"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">提交</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="name" label="层次名称"></el-table-column>
        <el-table-column prop="des" label="层次描述"> </el-table-column>
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
import { getSchoolLevelList,addSchoolLevel,updateSchoolLevel,deleteSchoolLevel } from "@/api/getData";
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
    this.getSchoolLevelListHttp();
  },
  methods: {
    async addSchoolLevelHttp() {
      console.log(this.form)
      const res = await addSchoolLevel(this.form)
      if (res.status == 1) {
        this.dialogVisible = false;
        this.$refs['form'].resetFields();
        this.getSchoolLevelListHttp();
      } else {
        throw new Error(res.message);  
      }
    },
    async updateSchoolLevelHttp(id) {
      const res = await updateSchoolLevel({id,...this.form})
      if (res.status == 1) {
        this.dialogVisible = false;
        this.$refs['form'].resetFields();
        this.getSchoolLevelListHttp();
      } else {
        throw new Error(res.message);
      }
    },
    async deleteSchoolLevelHttp(id) {
      const res = await deleteSchoolLevel(id)
      if(res.status == 1) {
        this.getSchoolLevelListHttp();
      }
    },
    handlerAdd() {
      this.dialogVisible = true;
      this.title = '新增学校层次';
      this.isAdd = true;
      this.$nextTick(() => {
        this.form.name = ''
        this.form.des = ''
      })
    },
    handleEdit(index,row) {
      this.dialogVisible = true;
      this.title = '修改学校层次';
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
          this.deleteSchoolLevelHttp(id)
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
        this.addSchoolLevelHttp()
      }else {
        this.updateSchoolLevelHttp({id:this.type_id,...this.form})
      }
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.offset = (val - 1) * this.limit;
      this.getSchoolLevelListHttp();
    },
    async getSchoolLevelListHttp() {
      try {
        const res = await getSchoolLevelList({
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


