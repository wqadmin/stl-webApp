<template>
  <a-modal
    title="排口"
    :width="980"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="saveClick"
    @cancel="handleCancel"
    :mask="true"
    :centered="true"
    :footer="null"
  >
    <a-spin :spinning="confirmLoading">
      <a-form class="from">
        <h3>名称</h3>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="内部编码">
              <a-input placeholder disabled v-model="list.innerCode"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="标准编码">
              <a-input placeholder  v-model="list.standardCode" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="内部名称">
              <a-input placeholder disabled v-model="list.innerName"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="标准名称">
              <a-input placeholder v-model="list.standardName"/>
            </a-form-item>
          </a-col>
        </a-row>
        <h3 style="margin-top: 10px;">位置</h3>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="所属河道">
              <a-select                
                :allowClear="true"
                placeholder="请输入河流"
                optionFilterProp="children"
                style="width: 100%"
                v-model="list.riverId"
                @change="riverChange"
              >
                <a-select-option
                  :value="item.id"
                  v-for="(item, index) in riverList"
                  :key="index"
                >{{item.name}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="排入水体功能区名称">
              <a-input placeholder v-model="list.functionName"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="河道序号">
              <a-input placeholder v-model="list.code" disabled/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="河道管理等级">
              <a-input placeholder v-model="list.supervisoryLevel" disabled/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="河道等级">
              <a-input placeholder v-model="list.priority" disabled/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="所属街道">
              <a-select
                :allowClear="true"
                placeholder="请输入街道"
                optionFilterProp="children"
                style="width: 100%"
                v-model="list.streetId"
                @change="streetChange"
              >
                <a-select-option
                  :value="item.id"
                  v-for="(item, index) in streetList"
                  :key="index"
                >{{item.name}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="一级河长">
              <a-input placeholder v-model="list.controller" disabled/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="二级河长">
              <a-input placeholder  v-model="list.tworiver" disabled/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="地标位置">
              <a-input placeholder v-model="list.landmarkLocation"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="地址">
               <a-input placeholder v-model="list.address" disabled/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="经度">
              <a-input placeholder v-model="list.lng" disabled/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="纬度">
              <a-input placeholder v-model="list.lat" disabled/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="准确位置">
              <a-input placeholder  v-model="list.accurateLocation"/>
            </a-form-item>
          </a-col>
        </a-row>
        <h3 style="margin-top: 10px;">属性</h3>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol" label="类别">
              <a-select  :allowClear="true" placeholder="请选择类别"  @change="typeChange" v-model="list.type">
                <a-select-option value="rainwater">雨水</a-select-option>
                <a-select-option value="life">生活</a-select-option>
                <a-select-option value="mix">混合</a-select-option>
                <a-select-option value="industrial">工业</a-select-option>
                <a-select-option value="powerplant">电厂温排水</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="排放方式">
              <a-select  :allowClear="true" placeholder="请选择" v-model="list.letway">
                <a-select-option value="sequence">连续</a-select-option>
                <a-select-option value="interrupt">间接</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="入河方式">
              <a-input placeholder v-model="list.enterRiverWay"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="大小">
              <a-input placeholder v-model="list.enterRiverSize"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="实际年排放总量(万吨)">
              <a-input placeholder v-model="list.yearLetSize"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="主要污染物">
              <a-input placeholder v-model="list.pollutant"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="设置单位">
              <a-input placeholder v-model="list.settingUnit"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="单位地址">
              <a-input placeholder v-model="list.unitAddress"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="联系人">
              <a-input placeholder v-model="list.linkman"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="联系地址">
              <a-input placeholder v-model="list.linktel"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="封堵状态">
              <a-input placeholder v-model="list.blockoffStatus"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="情况说明">
              <a-input placeholder v-model="list.statement"/>
            </a-form-item>
          </a-col>
        </a-row>
        <h3 style="margin-top: 10px;">等级情况</h3>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="建成时间">
              <a-input placeholder v-model="list.activateTime"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="登记情况">
              <a-input placeholder v-model="list.registrationState"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="批准情况">
              <a-input placeholder v-model="list.approveState"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="批准单位">
              <a-input placeholder v-model="list.approveUnit"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="排污许可">
              <a-input placeholder v-model="list.dischargeLicense"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"   label="许可证号">
              <a-input placeholder v-model="list.licenseNo"/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-row style="width:100%">
          <a-col :span="12">
            <a-form-item :label-col="labelCol" :wrapper-col="wrapperCol"  label="影像">
              <el-upload
                class="upload-demo"
                :data="upload"
                name="media"
                :headers="headers"
                action="/server/data/admin/mapdraw/media/save"
                :on-preview="handlePreview"
                :on-success="handleSuccess"
                :on-errore="handleRemove"
                :file-list="fileList"
                :limit='1'>
                <a-button type="primary" icon="plus" >上传</a-button>
              </el-upload>
              <viewer >
                <img v-for="item in attachmentJpg" :key="item.id" :src="item.media" alt="" style="height:70px;">
              </viewer >
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
      <a-divider orientation="left"></a-divider>
      <a-row style="width:100%; margin-top:10px;" type="flex" justify="space-around">
        <a-col :span="3">
          <a-button block @click="handleCancel">取消</a-button>
        </a-col>
        <a-col :span="3">
          <a-button block @click="saveClick">保存</a-button>
        </a-col>
      </a-row>
    </a-spin>
  </a-modal>
</template>

<script>
import moment from 'moment'
import Vue from 'vue'
import { ACCESS_TOKEN } from '@/store/mutation-types'
import { getRiverList,getStreetList,informationStreet,informationRiver,dischargeInner,mapdrawDischargeSave,mediaList} from '@/api/login'
export default {
  data() {
    return {
      fileList:[],
      file:false,
      headers: {
        Authorization: '',
        'X-TENANT-ID': 'jl:jlgis@2019' 
      },
      list:{
        riverId:'',
        streetId:'',
        tworiver:'',
        supervisoryLevel:'',
        controller:'',
        priority:'',
        lat:'',
        lng:'',
        type:'',
        waterName:'苏州',
        landmarkLocation:'',
        accurateLocation:'',
        innerCode:'',
        innerName:'',
        drawId:'',
        letway:'',
        enterRiverWay:'',
        enterRiverSize:'',
        yearLetSize:'',
        pollutant:'',
        settingUnit:'',
        unitAddress:'',
        linkman:'',
        linktel:'',
        blockoffStatus:'',
        statement:'',
        activateTime:'',
        registrationState:'',
        approveState:'',
        approveUnit:'',
        dischargeLicense:'',
        licenseNo:'',
        innerCode:'',
        innerName:'',
        functionName:'',
      },
      attachmentJpg:[],
      riverList:[],
      streetList:[],
      upload:{
        title:'1',
        drawId:'',
        id:'',
      },
      labelCol: {
        xs: { span: 18 },
        sm: { span: 6 }
      },
      wrapperCol: {
        xs: { span: 18 },
        sm: { span: 16 }
      },
      visible: false,
      confirmLoading: false,

    }
  },
  computed: {
  },
  mounted(){
    this.headers.Authorization=Vue.ls.get(ACCESS_TOKEN)
  },
  methods: {
    moment,
    getList(){
      getRiverList(this.$store.state.id).then(res=>{
        this.riverList = res.data.data
      })
      getStreetList(this.$store.state.id).then(res=>{
        this.streetList = res.data.data
      })
    },
    typeChange(value,option){
      if (this.list.riverId!='') {
        let data={
          riverId:this.list.riverId,
          type:value
        }
        dischargeInner(data).then(res=>{
          this.list.innerCode = res.data.innerCode
          this.list.innerName = res.data.innerName
        })
      }else{
        this.$message.warning('请先选择河道');
      }
    },
    riverChange(value,option){
      informationRiver(value).then(res=>{
        console.log(res.data);
         this.list.code = res.data.info.code
         this.list.controller = res.data.info.controller
         this.list.supervisoryLevel = res.data.info.supervisoryLevel.name
         if (res.data.info.priority == true) {
           this.list.priority='重点'
         }else if(res.data.info.priority == false){
           this.list.priority='非重点'
         }
      })
    },
    streetChange(value,option){
      informationStreet(value).then(res=>{
        this.list.tworiver = res.data.controller
      })
    },
    add(id,result) {
      this.headers.Authorization=Vue.ls.get(ACCESS_TOKEN)
      this.visible = true
      this.list.drawId =id
      this.upload.drawId =id
      this.list.address = result.formatted_address
      this.list.lat =  result.resultObj.location.lat
      this.list.lng =  result.resultObj.location.lon
      this.getList()
    },
    // 添加河流
    handleSubmit() {
      const {
        form: { validateFields }
      } = this
      this.confirmLoading = true
      validateFields((errors, values) => {
        if (!errors) {
          console.log('values', values)
          setTimeout(() => {
            this.visible = false
            this.confirmLoading = false
            this.$emit('ok', values)
          }, 1500)
        } else {
          this.confirmLoading = false
        }
      })
    },
    handleCancel() {
      this.visible = false
      this.list.riverId=''
      this.list.streetId=''
      this.list.tworiver=''
      this.list.supervisoryLevel=''
      this.list.controller=''
      this.list.priority=''
      this.list.lat=''
      this.list.lng=''
      this.list.type=''
      this.list.landmarkLocation=''
      this.list.accurateLocation=''
      this.list.innerCode=''
      this.list.innerName=''
      this.list.drawId=''
      this.list.letway=''
      this.list.enterRiverWay=''
      this.list.enterRiverSize=''
      this.list.yearLetSize=''
      this.list.pollutant=''
      this.list.settingUnit=''
      this.list.unitAddress=''
      this.list.linkman=''
      this.list.linktel=''
      this.list.blockoffStatus=''
      this.list.statement=''
      this.list.activateTime=''
      this.list.registrationState=''
      this.list.approveState=''
      this.list.approveUnit=''
      this.list.dischargeLicense=''
      this.list.licenseNo=''
      this.list.innerCode=''
      this.list.innerName=''
      this.list.functionName=''
      this.upload.id = ''
    },
    saveClick(){
      let data =this.list
      mapdrawDischargeSave(data).then(res=>{
        this.$message.success('保存成功')
        this.$parent.getMapdrawPage('1')
        this.handleCancel()
      }).catch(err => {
          this.$message.error(err.response.data.message);
      })
    },
    handleSuccess(response, file, fileList){
     this.$message.success('上传成功');
     this.fileList=[]
     mediaList(this.list.drawId).then(res=>{
       console.log(res.data);
       this.attachmentJpg = res.data
     })
    },
    handleRemove(err) {
      console.log(err);
    },
    handlePreview(file) {
      console.log(file);
    },
    handleExceed(files, fileList) {
      
    },
    beforeRemove(file, fileList) {
      
    }
  }
}
</script>
<style lang="less" scoped>
.ant-form-item {
  margin-bottom: 0;
}
.from {
  max-height: 450px;
  overflow-y: scroll;
  h3 {
    font-weight: 600;
    font-size: 15px;
  }
}
</style>
