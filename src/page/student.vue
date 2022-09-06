<!--
 * @Author: yangming
 * @LastEditTime: 2022-09-06 11:17:41
 * @Description: 
-->
<template>
  <div class="fillcontain">
    <head-top></head-top>
    <div class="table_container">
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="name" label="姓名" width="180">
        </el-table-column>
        <el-table-column prop="age" label="年龄" width="220">
        </el-table-column>
        <el-table-column prop="sex" label="性别" width="180">
        </el-table-column>
      </el-table>
      <div class="Pagination" style="text-align: left;margin-top: 10px;">
        <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="currentPage"
          :page-size="20" layout="total, prev, pager, next" :total="count">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import headTop from '../components/headTop'
import { getStudentList } from '@/api/getData'
export default {
  data() {
    return {
      tableData: [],
      currentRow: null,
      offset: 0,
      limit: 20,
      count: 0,
      currentPage: 1,
    }
  },
  components: {
    headTop,
  },
  created() {
    this.getStudent();
  },
  methods: {
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.offset = (val - 1) * this.limit;
      this.getStudent()
    },
    async getStudent() {
      try {
        const res = await getStudentList({ offset: this.offset, limit: this.limit });
        if (res.status == 1) {
          this.tableData = [];
          res.data.forEach(item => {
            const tableItem = {
              name: item.name,
              age: item.age,
              sex: item.sex,
            }
            this.tableData.push(tableItem)
          })
        } else {
          throw new Error(res.message)
        }
      } catch (err) {
        console.log('获取数据失败', err);
      }
    }
  },
}
</script>

<style lang="less">
@import '../style/mixin';

.table_container {
  padding: 20px;
}
</style>


