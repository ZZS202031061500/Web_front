<template>
  <div id="app">
    <el-container>
      <el-header height="100px">
        <h1>联系人管理系统</h1>
      </el-header>
      <el-main>
        <el-table
          border
          :data="tableData"
          style="width: 100%"
          height="600">
          <el-table-column
            fixed
            prop="contactId"
            label="ID"
            align="center">
          </el-table-column>
          <el-table-column
            prop="contactName"
            align="center"
            label="姓名">
          </el-table-column>
          <el-table-column
            prop="sex"
            align="center"
            label="性别">
          </el-table-column>
          <el-table-column
            prop="contactPhone"
            align="center"
            label="手机号">
          </el-table-column>
          <el-table-column
            prop="contactBirthday"
            align="center"
            label="生日">
          </el-table-column>
          <el-table-column label="操作" align="center">
            <template slot="header">
              <el-button @click="dialogFormVisible1 = true" size="small">新增联系人</el-button>
              <el-button @click="getData" size="small">刷新数据</el-button>
            </template>
            <template scope="scope">
              <el-button
                size="mini"
                @click="showDialog(scope)">编辑
              </el-button>
              <el-button
                size="mini"
                type="danger"
                @click="open(scope.$index)">删除
              </el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-dialog title="修改信息" :visible.sync="dialogFormVisible">
          <el-form :model="tableData[index]">
            <el-form-item label="ID" :label-width="formLabelWidth">
              <el-input style="width: 300px" v-model="tableData[index].contactId" autocomplete="on" disabled></el-input>
            </el-form-item>
            <el-form-item label="姓名" :label-width="formLabelWidth">
              <el-input style="width: 300px" v-model="tableData[index].contactName" autocomplete="on"></el-input>
            </el-form-item>
            <el-form-item label="手机号" :label-width="formLabelWidth">
              <el-input style="width: 300px" v-model="tableData[index].contactPhone" autocomplete="on"></el-input>
            </el-form-item>
            <el-form-item label="生日" :label-width="formLabelWidth">
              <el-date-picker
                v-model="tableData[index].contactBirthday"
                type="date"
                style="width: 300px"
                format="yyyy-MM-dd"
                value-format="yyyy-MM-dd"
                placeholder="选择生日时间">
              </el-date-picker>
            </el-form-item>
            <el-form-item label="性别" :label-width="formLabelWidth">
              <el-select v-model="tableData[index].sex" placeholder="请选择性别" style="width: 300px">
                <el-option label="男" value="男"></el-option>
                <el-option label="女" value="女"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="change(index)">确 定</el-button>
          </div>
        </el-dialog>

        <el-dialog title="新增信息" :visible.sync="dialogFormVisible1">
          <el-form :model="newdata">
            <el-form-item label="ID" :label-width="formLabelWidth">
              <el-input style="width: 300px" v-model="newdata.contactId" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="姓名" :label-width="formLabelWidth">
              <el-input style="width: 300px" v-model="newdata.contactName" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="手机号" :label-width="formLabelWidth">
              <el-input style="width: 300px" v-model="newdata.contactPhone" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="生日" :label-width="formLabelWidth">
              <el-date-picker
                v-model="newdata.contactBirthday"
                type="date"
                style="width: 300px"
                format="yyyy-MM-dd"
                value-format="yyyy-MM-dd"
                placeholder="选择生日时间">
              </el-date-picker>
            </el-form-item>
            <el-form-item label="性别" :label-width="formLabelWidth">
              <el-select v-model="newdata.sex" placeholder="请选择性别" style="width: 300px">
                <el-option label="男" value="男"></el-option>
                <el-option label="女" value="女"></el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible1 = false">取 消</el-button>
            <el-button type="primary" @click="add">确 定</el-button>
          </div>
        </el-dialog>
      </el-main>
    </el-container>

  </div>
</template>

<script>

import axios from 'axios'

export default {
  name: 'App',
  components: {},
  data () {
    return {
      tableData: [],
      dialogFormVisible: false,
      dialogFormVisible1: false,
      formLabelWidth: '120px',
      index: 0,
      newdata: {
        contactId: null,
        contactName: '',
        contactPhone: '',
        contactBirthday: '',
        sex: ''
      }
    }
  },
  methods: {
    add () {
      this.dialogFormVisible1 = false
      axios.post('http://101.34.24.163:8081/contact', JSON.stringify(this.newdata), {
        headers: {
          'Content-Type': 'application/json'
        }
      }).then(() => {
        this.getData()
      })
    },
    change (index) {
      this.dialogFormVisible = false
      axios.put('http://101.34.24.163:8081/contact', JSON.stringify(this.tableData[index]), {
        headers: {
          'Content-Type': 'application/json'
        }
      })
    },
    open (index) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        await axios.delete(`http://101.34.24.163:8081/contact/${this.tableData[index].contactId}`)
        this.tableData.splice(index, 1)
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    showDialog (scope) {
      this.index = scope.$index
      // this.tableData.filter(item => item.contactId === in)
      console.log(scope.row)
      this.dialogFormVisible = true
    },
    getData () {
      axios.get('http://101.34.24.163:8081/contact').then(res => {
        this.tableData = res.data.data
      })
    }
  },
  mounted () {
    this.getData()
  }
}
</script>

<style lang="less">
.el-container {
  width: 90%;
  margin: 20px auto;
}

.el-header {
  background: linear-gradient(135deg,#17ead9,#6078ea);
  text-align: center;
  line-height: 100px;

  h1 {
    color: #ebeee8;
  }
}

* {
  margin: 0;
  padding: 0;
}
</style>
