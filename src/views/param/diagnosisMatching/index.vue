<template>
  <div class="app-container">
    <el-card>
      <div>
        <el-input v-model="listQuery.diaMatParameterCde" style="width: 200px;" placeholder="请输入诊断匹配参数码查询" />
        <el-input v-model="listQuery.explCategort" style="width: 200px;" placeholder="请输入说明查询" />
        <el-button style="margin-left: 10px;" type="success" icon="el-icon-search" @click="fetchData">查询</el-button>
        <el-button style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleSave">添加</el-button>
        <el-button style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleRoute">明细</el-button>
      </div>
      <br>
      <el-table v-loading="listLoading" :data="list" element-loading-text="Loading" border fit highlight-current-row @selection-change="handleSelect">
        <el-table-column
          type="selection"
          width="55"
        />
        <el-table-column align="center" label="序号" width="95">
          <template slot-scope="scope">
            {{ scope.$index +1 }}
          </template>
        </el-table-column>

        <el-table-column align="center" label="诊断匹配参数码" width="450">
          <template slot-scope="scope">
            {{ scope.row.diaMatParameterCde }}
          </template>
        </el-table-column>

        <el-table-column align="center" label="说明" width="450">
          <template slot-scope="scope">
            {{ scope.row.explCategort }}
          </template>
        </el-table-column>

        <el-table-column align="center" label="操作" fixed="right">
          <template slot-scope="scope">
            <el-button type="primary" size="mini" icon="el-icon-edit" @click="handleEdit(scope.row.id)">编辑</el-button>
            <el-button type="danger" size="mini" icon="el-icon-delete" class="action-button" @click="handleDel(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <save :son-data="form" :business-data="businessData" @sonStatus="status" />

      <pagination
        v-show="total>0"
        :total="total"
        :page.sync="listQuery.pageNum"
        :limit.sync="listQuery.pageSize"
        @pagination="fetchData"
      />
    </el-card>
  </div>
</template>

<script>
import { getList, findById, del, getPath } from '@/api/base'
// mport { getCodeList } from '@/api/code'
import Pagination from '@/components/Pagination'
import Save from './save'

export default {
  components: { Pagination, Save },
  data() {
    return {
      list: null,
      listLoading: true,
      basePath: 'diagnosisMatching',
      listQuery: {
        pageNum: 1,
        pageSize: 10,
        diaMatParameterCde: '',
        sort: '+id'
      },
      total: 0,
      dialogVisible: false,
      form: null,
      businessData: {}
    }
  },
  created() {
    /* if (this.$route.query.pubCoverId) { // 上级页面传入参数
        this.listQuery.pubCoverId = this.$route.query.pubCoverId
      }*/
    this.fetchData()
    // this.fetchTypeData()
  },
  mounted() {
  },
  methods: {
    handleRoute() {
      debugger
      if (this.selected.length !== 1) {
        this.$message({
          showClose: true,
          message: '只能选择一条查看',
          type: 'warning'
        })
      } else {
        getPath({ diaMatParameterCde: this.selected[0].diaMatParameterCde }).then(res => {
          var typPath = res.data.typPath
          this.$router.push({ path: '/param/' + typPath, query: { diaMatParameterCde: this.selected[0].diaMatParameterCde }})
        })
      }
    },
    handleSelect(data) {
      this.selected = data
    },
    _notify(message, type) {
      this.$message({
        message: message,
        type: type
      })
    },
    fetchData() {
      this.listLoading = true
      getList(this.basePath, this.listQuery).then(response => {
        this.list = response.data.data
        this.total = response.data.total
        this.listLoading = false
      })
    },
    /*,
    fetchTypeData() {
      // 获取codeList
      getCodeList({ parent: ['CExplCdeSubcategory'] }).then(res => {
        this.businessData = res.data
        // 组装table 的map
        for (const key in this.businessData) {
          this.businessData[key].forEach(item => {
            this[key][item.value] = item.label
          })
        }
      })
    },*/
    handleSave() {
      this.form = { id: null }
      /* if (this.$route.query.pubCoverId) { // 上级页面传入参数
          this.form.pubCoverId = this.$route.query.pubCoverId
        }*/
      this.dialogVisible = true
    },
    handleEdit(id) {
      // 跳转到新的页面
      findById(this.basePath, id).then(response => {
        this.form = response.data
      })
    },

    // 子组件的状态Flag，子组件通过`this.$emit('sonStatus', val)`给父组件传值
    // 父组件通过`@sonStatus`的方法`status`监听到子组件传递的值
    status(data) {
      if (data) {
        this.fetchData()
      }
    },

    handleDel(id) {
      this.$confirm('你确定永久删除此数据？, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        del(this.basePath, id).then(response => {
          if (response.code === 200) {
            this._notify(response.msg, 'success')
          } else {
            this._notify(response.msg, 'error')
          }
          this.fetchData()
        })
      }).catch(() => {
        this._notify('已取消删除', 'info')
      })
    }
  }
}
</script>
