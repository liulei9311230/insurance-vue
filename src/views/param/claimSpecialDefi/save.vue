<template>
  <el-dialog :title="dialogTitle" :before-close="handleClose" :visible.sync="dialogVisible" width="55%">
    <el-form ref="form" :inline="true" :rules="rules" :model="form" status-icon label-position="right" label-width="80px">
      <el-form-item label="悬挂规则码/特殊审核要求码" prop="specialAuditsCde" label-width="120px">
        <el-input v-model="form.specialAuditsCde" placeholder="请输入悬挂规则码/特殊审核要求码" />
      </el-form-item>
      <el-form-item label="悬挂类型" prop="specialTyp" label-width="120px">
        <el-input v-model="form.specialTyp" placeholder="请输入悬挂类型" />
      </el-form-item>
      <el-form-item label="悬挂层级" prop="suspendLevel" label-width="120px">
        <el-input v-model="form.suspendLevel" placeholder="请输入悬挂层级" />
      </el-form-item>

      <el-form-item label="悬挂解释" prop="suspendExpain" label-width="120px">
        <el-input v-model="form.suspendExpain" placeholder="请输入悬挂解释" />
      </el-form-item>

      <el-form-item label="描述" prop="specialDesc" label-width="120px">
        <el-input v-model="form.specialDesc" placeholder="请输入描述" />
      </el-form-item>

      <el-form-item label="审阅层级" prop="specialLevel" label-width="120px">
        <el-input v-model="form.specialLevel" placeholder="请输入审阅层级" />
      </el-form-item>

      <el-form-item label="悬挂条件" prop="suspendCondition" label-width="120px">
        <el-input v-model="form.suspendCondition" placeholder="请输入悬挂条件" />
      </el-form-item>

      <el-form-item label="客户解释码" prop="suspendStatu" label-width="120px">
        <el-input v-model="form.suspendStatu" placeholder="请输入客户解释码" />
      </el-form-item>

      <el-form-item label="保单特殊人工操作" prop="speicalManualOperation" label-width="120px">
        <el-input v-model="form.speicalManualOperation" placeholder="请输入保单特殊人工操作" />
      </el-form-item>

      <el-form-item label="人工操作" prop="manualOperation" label-width="120px">
        <el-input v-model="form.manualOperation" placeholder="请输入人工操作" />
      </el-form-item>

      <el-form-item label="备注" prop="specialRemark" label-width="120px">
        <el-input v-model="form.specialRemark" placeholder="请输入备注" />
      </el-form-item>

    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="handleClose">
        Cancel
      </el-button>
      <el-button type="primary" @click="onSubmit('form')">
        Confirm
      </el-button>
    </div>
  </el-dialog>
</template>

<script>
import { save, edit } from '@/api/base'

export default {
  // 父组件向子组件传值，通过props获取。
  // 一旦父组件改变了`sonData`对应的值，子组件的`sonData`会立即改变，通过watch函数可以实时监听到值的变化
  // `props`不属于data，但是`props`中的参数可以像data中的参数一样直接使用
  props: ['sonData', 'businessData'],
  data() {
    return {
      dialogVisible: false,
      dialogTitle: '新增',
      basePath: 'claimSpecialDefi',
      form: {
        id: '',
        specialAuditsCde: '',
        specialTyp: '',
        suspendLevel: '',
        suspendExpain: '',
        specialDesc: '',
        specialLevel: '',
        suspendCondition: '',
        suspendStatu: '',
        manualOperation: '',
        speicalManualOperation: '',
        specialRemark: ''
      },
      rules: {
        sociInsuAreaNo: [{ required: true, trigger: 'blur', message: '请输入悬挂规则码/特殊审核要求码' }],
        specialTyp: [{ required: true, trigger: 'blur', message: '请输入悬挂类型' }],
        suspendLevel: [{ required: true, trigger: 'blur', message: '请输入悬挂层级' }],
        suspendExpain: [{ required: true, trigger: 'blur', message: '请输入悬挂解释' }],
        specialDesc: [{ required: true, trigger: 'blur', message: '请输入描述' }],
        specialLevel: [{ required: true, trigger: 'blur', message: '请输入审阅层级' }],
        suspendCondition: [{ required: true, trigger: 'blur', message: '请输入悬挂条件' }],
        suspendStatu: [{ required: true, trigger: 'blur', message: '请输入客户解释码' }],
        speicalManualOperation: [{ required: true, trigger: 'blur', message: '请输入保单特殊人工操作' }],
        manualOperation: [{ required: true, trigger: 'blur', message: '请输入人工操作' }]
      }
    }
  },
  watch: {
    'sonData': function(newVal, oldVal) {
      this.form = newVal
      this.dialogVisible = true
      if (newVal.id != null) {
        this.dialogTitle = 'Edit'
      } else {
        this.dialogTitle = 'Add'
      }
    }
  },
  methods: {
    _notify(message, type) {
      this.$message({
        message: message,
        type: type
      })
    },
    clearForm() {
      this.form.id = null
      this.form.specialAuditsCde = null
      this.form.specialTyp = null
      this.form.suspendLevel = null
      this.form.suspendExpain = null
      this.form.specialDesc = null
      this.form.specialLevel = null
      this.form.suspendCondition = null
      this.form.suspendStatu = null
      this.form.manualOperation = null
      this.form.speicalManualOperation = null
      this.form.specialRemark = null
    },
    handleClose() {
      this.clearForm()
      this.dialogVisible = false
    },
    onSubmit(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          if (this.form.id === null) {
            save(this.basePath, this.form).then(response => {
              if (response.code === 200) {
                this._notify(response.msg, 'success')
                this.clearForm()
                this.$emit('sonStatus', true)
                this.dialogVisible = false
              } else {
                this._notify(response.msg, 'error')
              }
            })
          } else {
            edit(this.basePath, this.form).then(response => {
              if (response.code === 200) {
                this._notify(response.msg, 'success')
                this.clearForm()
                this.$emit('sonStatus', true)
                this.dialogVisible = false
              } else {
                this._notify(response.msg, 'error')
              }
            })
          }
        } else {
          this.$message('error submit!!')
          return false
        }
      })
    }
  }
}
</script>

<style lang="css">
  .line {
    text-align: center;
  }

  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }

  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }

  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }

  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>

