<template>
  <a-modal
    title="添加督办单"
    :width="640"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
    :centered="true"
  >
    <a-spin :spinning="confirmLoading">
      <a-form>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="督办单名称">
          <a-input placeholder="请输入督办单名称" v-model="list.name"/>
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="所属街道" has-feedback>
          <a-select
            showSearch
            :allowClear="true"
            placeholder="请输入街道"
            optionFilterProp="children"
            style="width: 100%"
            @change="handleChange1"
            :filterOption="filterOption"
            v-model="list.streetId"
          >
            <a-select-option
              :value="item.id"
              v-for="(item, index) in streetList"
              :key="index"
            >{{item.name}}</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="所属河流" has-feedback>
          <a-select
            showSearch
            mode="multiple"
            :allowClear="true"
            placeholder="请输入河流" 
            optionFilterProp="children"
            style="width: 100%"
            @change="handleChange2"
            :filterOption="filterOption"
            v-model="riverId"
          >
            <a-select-option
              :value="item.id"
              v-for="(item, index) in riverList"
              :key="index"
            >{{item.name}}</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="风险源类型" has-feedback>
          <a-select
            showSearch
            mode="multiple"
            :allowClear="true"
            placeholder="请输入风险源"
            optionFilterProp="children"
            style="width: 100%"
            @change="handleChange3"
            :filterOption="filterOption"
            v-model="riskSourceTypeId"
          >
            <a-select-option
              :value="item.id"
              v-for="(item, index) in labelList"
              :key="index"
            >{{item.name}}</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="文件" has-feedback>
          <el-upload
            class="upload-demo"
            ref="upload"
            :data="list"
            name="attachment"
            :headers="headers"
            action="/server/data/admin/supervision/save"
            :on-preview="handlePreview"
            :on-success="handleSuccess"
            :on-change="uploadChange"
            :on-remove="handleRemove"
            :file-list="fileList"
            :limit='1'
            :auto-upload="false">
            <a-button type="primary" icon="plus" >添加</a-button>
          </el-upload>
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="调查日期" has-feedback>
          <!-- <a-date-picker   :value="list.surveyDate" :defaultValue="moment('2015-01-01', 'YYYY-MM-DD')"/> -->
          <el-date-picker v-model="list.surveyDate" type="date" placeholder="选择日期" style="width: 100%"  format="yyyy-MM-dd" @change="onChange"></el-date-picker>
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="标签" has-feedback>
            <a-input placeholder="使用逗号分隔"  v-model="list.tags" />
        </a-form-item>
        <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="备注" has-feedback>
            <a-input placeholder=""  v-model="list.remark"/>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import Vue from 'vue'
import { ACCESS_TOKEN } from '@/store/mutation-types'
const OPTIONS = ['Apples', 'Nails', 'Bananas', 'Helicopters']
import {SuperviseSave,SuperviseDetail} from '@/api/login'
export default {
  props:{
    streetList: Array,
    riverList: Array,
    labelList: Array,
  },
  data() {
    return {
      fileList:[],
      file:false,
      list:{
        id:'',
        projectId:this.$store.state.id,
        name:'',
        streetId:'',
        riverId:'',
        riskSourceTypeId:'',
        surveyDate:'',
        remark:'',
        tags:'',
      },
      streetId:[],
      riverId:[],
      riskSourceTypeId:[],
      labelCol: {
        xs: { span: 24 },
        sm: { span: 5 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 16 }
      },
      visible: false,
      confirmLoading: false,
      headers: {
        Authorization: '',
        'X-TENANT-ID': 'jl:jlgis@2019' 
      },
      form: this.$form.createForm(this)
    }
  },
  computed: {
    filteredOptions() {
      return OPTIONS.filter(o => !this.selectedItems.includes(o))
    }
  },
  mounted(){
    this.headers.Authorization=Vue.ls.get(ACCESS_TOKEN)
  },
  methods: {
    add() {
      this.visible = true
    },
    add1(id) {
      this.visible = true
      SuperviseDetail(id).then(res => {
        var arr = res.data
        function formatDate(now) { 
          var year=now.getFullYear() //取得4位数的年份
          var month=now.getMonth()+1  //取得日期中的月份，其中0表示1月，11表示12月
          var date=now.getDate()      //返回日期月份中的天数（1到31）
          var hour=now.getHours()     //返回日期中的小时数（0到23）
          var minute=now.getMinutes() //返回日期中的分钟数（0到59）
          var second=now.getSeconds() //返回日期中的秒数（0到59）
          return year+"-"+month+"-"+date
        }
        this.list.name = arr.name
        this.list.surveyDate = formatDate(new Date(arr.surveyDate))
        this.list.id= arr.id
        arr.tags1=''
        if (arr.tags !=null) {
          arr.tags.forEach(v => {
            arr.tags1 = arr.tags1 + v +','
          });
        }
        arr.riskSourceType1=[]
        arr.rivers1=[]
        if (arr.riskSourceType!=null) {
          for (let i = 0; i < arr.riskSourceType.length; i++) {
            arr.riskSourceType1.push(arr.riskSourceType[i].id)
          }
        }
        if (arr.rivers!=null) {
          for (let i = 0; i < arr.rivers.length; i++) {
            arr.rivers1.push(arr.rivers[i].id)
          }
        }
        this.riskSourceTypeId =arr.riskSourceType1
        this.riverId =arr.rivers1
        this.list.streetId =arr.street.id
        this.list.tags =arr.tags1
        console.log(arr);
      }).catch(err => {
        
      })
    },
    // 添加河流
    addRiver(value) {
      console.log(value)
    },
    // 风险源类型
    handleChange(selectedItems) {
      this.selectedItems = selectedItems
      console.log(selectedItems)
    },
    handleChange1(index) {
      console.log(`selected ${index}`)
      this.streetList.forEach(value => {
        if (value.name === index) {
          value.clicked = true
        } else {
          value.clicked = false
        }
      })
    },
    handleChange2(index) {
      console.log(`selected ${index}`)
      this.riverList.forEach(value => {
        if (value.name === index) {
          value.clicked = true
        } else {
          value.clicked = false
        }
      })
    },
    handleChange3(index) {
      console.log(`selected ${index}`)
      this.labelList.forEach(value => {
        if (value.name === index) {
          value.clicked = true
        } else {
          value.clicked = false
        }
      })
    },
    filterOption(input, option) {
      return option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0
    },
    handleCancel() {
      this.visible = false
      this.list.id=''
      this.list.name=''
      this.list.streetId=''
      this.list.riverId=''
      this.list.riskSourceTypeId=''
      this.streetId=[]
      this.riverId=[]
      this.riskSourceTypeId=[]
      this.list.surveyDate=''
      this.list.remark=''
      this.list. tags=''
    },
    onChange(date){
      function formatDate(now) { 
        var year=now.getFullYear() //取得4位数的年份
        var month=now.getMonth()+1  //取得日期中的月份，其中0表示1月，11表示12月
        var date=now.getDate()      //返回日期月份中的天数（1到31）
        var hour=now.getHours()     //返回日期中的小时数（0到23）
        var minute=now.getMinutes() //返回日期中的分钟数（0到59）
        var second=now.getSeconds() //返回日期中的秒数（0到59）
        return year+"-"+month+"-"+date
      }
      this.list.surveyDate=formatDate(date)
    },
    handleSubmit() {
      this.list.riverId=this.riverId.join(',')
      this.list.riskSourceTypeId=this.riskSourceTypeId.join(',')
      if (this.fileList.length == 0) {
        var data = this.list
        SuperviseSave(data).then(res => {
            this.$message.success('保存成功')
            this.handleCancel()
            this.$parent.$parent.getPage();
        }).catch(err => {
          this.$message.error(err.response.data.message)
        })
      }else{
        this.$refs.upload.submit();
      }
    },
    handleSuccess(response, file, fileList){
      this.$message.success('保存成功');
      this.handleCancel();
      this.$parent.getPage();
    },
    uploadChange(file, fileList){
      if(this.fileList.length==0){
        this.fileList=fileList
      }else{
        this.fileList=[]
      }
      this.attachmentJpg=window.URL.createObjectURL(file.raw)
    },
    handleRemove(file, fileList) {
      
    },
    handlePreview(file) {
    }
  }
}
</script>
<style lang="less" >
.ant-form input[type='file'] {
  display: none; 
}
</style>
<style scoped>
.ant-form-item {
  margin-bottom: 0;
}
</style>
