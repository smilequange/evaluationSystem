<template>
  <a-drawer
    :title="title"
    :maskClosable="true"
    :width="800"
    placement="right"
    :closable="true"
    @close="handleCancel"
    :visible="structModalVisible"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item label="结构体名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input placeholder="请输入结构体名称" :disabled="disableSubmit" v-decorator="[ 'structName', validatorRules.structName]"/>
        </a-form-item>
        <a-form-item label="结构体备注" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input placeholder="请输入结构体备注" :disabled="disableSubmit" v-decorator="[ 'structRemark', validatorRules.structRemark]"/>
        </a-form-item>
        <!--<a-form-item label="结构体" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
          <a-row :gutter="15">
            <a-col :md="5" :sm="4" offset="2">
              <span>结构体变量名</span>
            </a-col>
            <a-col :md="5" :sm="4">
              <span>参数类型</span>
            </a-col>
            <a-col :md="5" :sm="4">
              <span>参数单位</span>
            </a-col>
            <a-col :md="5" :sm="4">
              <span>参数含义</span>
            </a-col>
          </a-row>
        <!--</a-form-item>-->
        <a-form-item v-for="(item, index) in structModalListData" :key="index">
          <a-row :gutter="15">
            <a-col :md="5" :sm="4" offset="2">
              <a-input v-model="item.structVariable" :disabled="disableSubmit"/>
            </a-col>
            <a-col :md="5" :sm="4">
              <a-input v-model="item.structParaType" :disabled="disableSubmit"/>
            </a-col>
            <a-col :md="5" :sm="4">
              <a-input v-model="item.structParaUnit" :disabled="disableSubmit"/>
            </a-col>
            <a-col :md="5" :sm="4">
              <a-input v-model="item.structParaMeaning" :disabled="disableSubmit"/>
            </a-col>
            <a-icon type="plus-circle" v-if="!disableSubmit"  class="structAddIcon" @click="addStructInfo(index)"/>
            <a-icon type="delete" v-if="!disableSubmit" class="structDeleteIcon" @click="deleteStructInfo(index)"/>
          </a-row>
        </a-form-item>

      </a-form>
    </a-spin>

    <div class="drawer-bootom-button" v-show="!disableSubmit">
      <a-popconfirm title="确定放弃编辑？" @confirm="handleCancel" okText="确定" cancelText="取消">
        <a-button style="margin-right: .8rem">取消</a-button>
      </a-popconfirm>
      <a-button @click="handleSubmit" type="primary" :loading="confirmLoading">提交</a-button>
    </div>
  </a-drawer>
</template>

<script>

  import AFormItem from "ant-design-vue/es/form/FormItem";
  import ARow from "ant-design-vue/es/grid/Row";
  import ACol from "ant-design-vue/es/grid/Col";
  import AInput from "ant-design-vue/es/input/Input";

  export default {
    name: "StructInfoModal",
    components: {
      AInput,
      ACol,
      ARow,
      AFormItem

    },
    data () {
      return{
        title: '',
        structModalVisible: false,
        confirmLoading: false,
        disableSubmit: false,
        form: this.$form.createForm(this),
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        validatorRules:{
          structName:{
            rules: [
              { required: true, message: '请输入结构体名称!' },
            ]},
          structRemark:{
            rules: [
              { required: true, message: '请输入结构体备注!'},
            ]}
        },
        structModalListData: [
          {
            structVariable: '',
            structParaType: '',
            structParaUnit: '',
            structParaMeaning: ''
          }
        ]
      }
    },
    created () {

    },
    computed:{

    },
    methods: {
      handleCancel () {
        this.structModalVisible = false;
      },
      handleSubmit () {
        this.form.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values);
            this.classificationModalVisible = false;
          }
        });
      },
      addStructInfo (index) {
        console.log('addStructInfo index:', index)
        const self = this;
        self.structModalListData.splice(index+1,0,{});
      },
      deleteStructInfo (index) {
        const self = this;
        if (self.structModalListData.length === 1) {
          this.$notification.open({
            message: '提示',
            description: '最后一行无法删除！',
            duration: 2,
            style: {
              width: '600px',
              marginLeft: `${335 - 600}px`
            },
          });
          return false;
        }
        self.structModalListData.splice(index,1);
      }
    }
  }
</script>

<style scoped>
  .structDeleteIcon{
    font-size: 18px;
  }
  .structDeleteIcon:hover{
    cursor:pointer;
  }
  .structAddIcon{
    font-size: 18px;
    margin-right: 10px;
  }
  .structAddIcon:hover{
    cursor:pointer;
  }
  .drawer-bootom-button {
    position: absolute;
    bottom: 0;
    width: 100%;
    border-top: 1px solid #e8e8e8;
    padding: 10px 16px;
    text-align: right;
    left: 0;
    background: #fff;
    border-radius: 0 0 2px 2px;
  }
</style>