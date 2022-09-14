<!--
 * @Author: yangming
 * @LastEditTime: 2022-09-14 22:25:41
 * @Description: 
-->
<template>
  <div class="fillcontain">
    <head-top></head-top>
    <div class="table_container">
      <div style="
          display: flex;
          flex-direction: row-reverse;
          align-items: center;
          height: 60px;
        ">
        <el-button type="primary" @click="handleAdd">新增</el-button>
      </div>
      <el-dialog :title="title" :visible.sync="dialogVisible" size="tiny">
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item label="省内排名">
            <el-input-number v-model="form.province_rank"  :min="1" :max="100"></el-input-number>
          </el-form-item>
          <el-form-item label="院校">
            <el-input v-model="form.school_name"></el-input>
          </el-form-item>
          <el-form-item label="类型">
            <el-select v-model="form.school_type_id" placeholder="请选择类型" style="width:100%;">
              <el-option v-for="item in schoolTypeList" :key="item.id" :label="item.name" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="所在地">
            <el-input v-model="form.school_address"></el-input>
          </el-form-item>
          <el-form-item label="性质">
            <el-input v-model="form.school_property"></el-input>
          </el-form-item>
          <el-form-item label="层次">
            {{form.school_level}}
            <el-select multiple  v-model="form.school_level" placeholder="请选择层次" style="width:100%;">
              <el-option v-for="item in schoolLevelList" :key="item.id" :label="item.name" :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
          <!-- <el-form-item label="学校描述">
            <el-input v-model="form.school_des"></el-input>
          </el-form-item> -->
          <el-form-item label="全国排名">
            <el-input-number v-model="form.nationwide_rank"  :min="1" :max="100"></el-input-number>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">提交</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-form>
      </el-dialog>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="province_rank" label="省内排名"> </el-table-column>
        <el-table-column prop="school_name" label="院校"></el-table-column>
        <el-table-column prop="school_type_name" label="类型"> </el-table-column>
        <el-table-column prop="school_address" label="所在地"></el-table-column>
        <el-table-column prop="school_property" label="性质"> </el-table-column>
        <el-table-column prop="school_level" label="层次"></el-table-column>
        <!-- <el-table-column prop="school_des" label="学校描述"> </el-table-column> -->
        <el-table-column prop="nationwide_rank" label="全国排名"> </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="Pagination" style="text-align: right; margin-top: 10px">
        <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage"
          :page-sizes="[10, 20, 30, 40]" :page-size="10" layout="total, sizes, prev, pager, next, jumper"
          :total="count">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import headTop from "../components/headTop";
import { getSchoolList, getSchoolTypeList,getSchoolLevelList, addSchool, updateSchool, deleteSchool } from "@/api/getData";
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
        province_rank: 0,
        school_name: "",
        school_type_name: "",
        school_type_id: '',
        school_address: '',
        school_property: '',
        school_level: [],
        school_des: '',
        nationwide_rank: 0
      },
      school_id: 1,
      isAdd: true,
      title: '新增学校',
      schoolTypeList: [],
      schoolLevelList: [],
    };
  },
  components: {
    headTop,
  },
  created() {
    this.getSchool();
    this.getSchoolTypeListHttp()
    this.getSchoolLevelListHttp()
  },
  methods: {
    async getSchoolLevelListHttp() {
      try {
        const res = await getSchoolLevelList({
          offset: 0,
          limit: 1000,
        });
        if (res.status == 1) {
          this.schoolLevelList = res.data;
        } else {
          throw new Error(res.message);
        }
      } catch (err) {
        console.log("获取数据失败", err);
      }
    },
    async getSchoolTypeListHttp() {
      try {
        const res = await getSchoolTypeList({
          offset: 0,
          limit: 1000,
        });
        if (res.status == 1) {
          this.schoolTypeList = res.data;
        } else {
          throw new Error(res.message);
        }
      } catch (err) {
        console.log("获取数据失败", err);
      }
    },
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
    async updateSchoolHttp(id) {
      const res = await updateSchool({ id, ...this.form })
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
      if (res.status == 1) {
        this.getSchool();
      }
    },
    handleAdd() {
      this.dialogVisible = true;
      this.title = '新增学校';
      this.isAdd = true;
      this.$nextTick(() => {
        this.form.school_name = ''
        this.form.school_des = ''
      })
    },
    handleEdit(index, row) {
      this.dialogVisible = true;
      this.title = '修改学校';
      this.isAdd = false;
      this.form = row;
      this.school_id = row.id;
    },
    handleDelete(index, { id }) {
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
      if (this.isAdd) {
        this.addSchoolHttp()
      } else {
        this.updateSchoolHttp({ id: this.school_id, ...this.form })
      }
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


