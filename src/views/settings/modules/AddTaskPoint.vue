<template>
  <a-modal
    title="添加任务点"
    :width="400"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @cancel="handleCancel"
    :mask="true"
    :centered="true"
    :footer="null"
  >
    <a-spin :spinning="confirmLoading">
      <a-row style="width:100%" type="flex" justify="space-between" align="middle">
        <a-col :span="6">任务点名称</a-col>
        <a-col :span="18">
          <a-input placeholder="任务点名称" v-model="list.name"/>
        </a-col>
      </a-row>
      <a-row style="width:100%;margin-top:10px;" type="flex" justify="space-between" align="top">
        <a-col :span="6">位置信息</a-col>
        <a-col :span="18">{{list.locationInfo}}</a-col>
      </a-row>
      <a-row style="width:100%;margin-top:10px;" type="flex" justify="space-between" align="top">
        <a-col :span="6">所属河道</a-col>
        <a-col :span="18">
          <span>已为您匹配到相关河道, 您可以手动添加或删除河道</span>
          <a-col :span="24" style="margin-top:10px;">
            <a-select
              showSearch
              mode="multiple"
              :allowClear="true"
              placeholder="请输入河流添加"
              optionFilterProp="children"
              style="width: 100%"
              @change="handleChange"
              :filterOption="filterOption"
              v-model="riverId"
            >
              <a-select-option
                :value="item.id"
                v-for="(item, index) in riverList"
                :key="index"
              >{{item.name}}</a-select-option>
            </a-select>
          </a-col>
        </a-col>
      </a-row>
      <a-row style="width:100%;margin-top:10px;" type="flex" justify="space-between" align="middle">
        <a-col :span="6">月计划次数</a-col>
        <a-col :span="18">
          <a-input placeholder="请输入月计划次数" v-model="list.times"/>
        </a-col>
      </a-row>
      <a-row style="width:100%; margin-top:10px;" type="flex" justify="space-around">
        <a-col :span="6">
          <a-button type="primary" block @click="handleCancel">取消</a-button>
        </a-col>
        <a-col :span="6">
          <a-button type="primary" block @click="handSave">保存</a-button>
        </a-col>
      </a-row>
    </a-spin>
  </a-modal>
</template>

<script>
const OPTIONS = ['Apples', 'Nails', 'Bananas', 'Helicopters']
import { taskPointDetail, taskPointSave,getRiverList} from '@/api/login'
export default {
  data() {
    return {
      list:{
        id:'',
        taskId:'',
        name:'',
        coordinate:'',
        altitude:'',
        locationInfo:'',
        times:'',
        riverId:'',
      },
      riverId:[],
      labelCol: {
        xs: { span: 18 },
        sm: { span: 6 }
      },
      wrapperCol: {
        xs: { span: 6 },
        sm: { span: 18 }
      },
      defaultRiver: '黄浦江',
      riverList: [
        // {
        //   id: 0,
        //   name: '黄浦江',
        //   clicked: true
        // },
        // {
        //   id: 1,
        //   name: '大治河',
        //   clicked: false
        // },
        // {
        //   id: 2,
        //   name: '川杨河',
        //   clicked: false
        // },
        // {
        //   id: 3,
        //   name: '蕰藻浜',
        //   clicked: false
        // },
        // {
        //   id: 4,
        //   name: '龙华港',
        //   clicked: false
        // },
        // {
        //   id: 5,
        //   name: '太浦河',
        //   clicked: false
        // },
        // {
        //   id: 6,
        //   name: '太湖',
        //   clicked: false
        // }
      ],
      visible: false,
      confirmLoading: false,

      selectedItems: [], //风险源类型
      form: this.$form.createForm(this)
    }
  },
  computed: {

  },
  mounted(){
    this.getRiver()
  },
  methods: {
    getRiver(){
      getRiverList(this.$store.state.id).then(res => {
          var arr = res.data.data
          this.riverList = arr
      }).catch(err => {})
    },
    //新建
    add(e,data,taskId) {
      console.log(e,data,taskId);
      this.list.coordinate = data.lon +','+data.lat
      this.list.taskId = taskId
      this.list.locationInfo = e
      this.visible = true
    },
    //保存
    handSave(){
      var data = this.list
      data.riverId = this.riverId.join(',')
      taskPointSave(data).then(res => {
        this.handleCancel()
        this.$message.success('保存成功')
        this.$parent.getList()
      }).catch(err => {
        this.$message.error(err.response.data.message)
      })
    },
    // 标签
    handleChangeTag(value) {
      console.log(`selected ${value}`)
    },
    //编辑
    edit(id){
      taskPointDetail(id).then(res => {
        console.log(res);
        var arr = res.data
        this.list.taskId = arr.taskInfo.id
        this.list.name = arr.name
        this.list.locationInfo = arr.locationInfo
        this.list.coordinate = arr.coordinate[0] +','+arr.coordinate[1]
        this.list.id = arr.id
        this.list.times=arr.times
        var riverId =[]
        for (const list of arr.rivers) {
          riverId.push(list.id)
        }
        this.riverId = riverId
        this.visible = true
      }).catch(err => {
        this.$message.error(err.response.data.message)
      })
    },
    handleCancel() {
      this.$parent.allPointTask()
      this.visible = false
      this.list.taskId=''
      this.list.id=''
      this.list.name=''
      this.list.coordinate=''
      this.list.altitude=''
      this.list.locationInfo=''
      this.list.times=''
      this.list.riverId=''
      this.riverId=[]
    },
    // 选择地址
    onChange(value, selectedOptions) {
      console.log(value, selectedOptions)
    },
    // 搜索
    handleChange(index) {
      console.log(`selected ${index}`)
      this.riverList.forEach(value => {
        if (value.name === index) {
          value.clicked = true
        } else {
          value.clicked = false
        }
      })
    },
    handleBlur() {
      console.log('blur')
    },
    handleFocus() {
      console.log('focus')
    },
    filterOption(input, option) {
      return option.componentOptions.children[0].text.toLowerCase().indexOf(input.toLowerCase()) >= 0
    },
    // 删除河道
    deleteRiver(index) {
      console.log(index)
      this.riverList.splice(this.riverList.findIndex(item => item.name === index), 1)
      this.$message.success('删除成功')
      this.defaultRiver = null
    }
  }
}
</script>
<style scoped>
.ant-form-item {
  margin-bottom: 0;
}
.from {
  max-height: 520px;
}
</style>
