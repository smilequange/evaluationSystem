<template>
  <a-drawer
    :title="title"
    :maskClosable="true"
    :width="600"
    placement="right"
    :closable="true"
    @close="handleCancel"
    :visible="wordMouldModalVisible"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item label="模板名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input placeholder="请输入模板名称" :disabled="disableSubmit" v-decorator="[ 'mouldName', validatorRules.mouldName]"/>
        </a-form-item>
        <a-form-item label="模板说明" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-input placeholder="请输入模板说明" :disabled="disableSubmit" v-decorator="[ 'mouldStatement', validatorRules.mouldStatement]"/>
        </a-form-item>
        <a-form-item label="选择模板" :labelCol="labelCol" :wrapperCol="wrapperCol">
          <a-upload name="file"
                    :multiple="false"
                    @change="UploadHandleChange"
                    v-decorator="[ 'mouldUploadFile', validatorRules.mouldUploadFile]"
          >
            <a-button>
              <a-icon type="upload" /> Click to Upload
            </a-button>
          </a-upload>
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
    name: "WordMouldInfoModal",
    components: {
      AInput,
      ACol,
      ARow,
      AFormItem

    },
    data () {
      return{
        title: '文件信息',
        wordMouldModalVisible: false,
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
          mouldName:{
            rules: [
              { required: true, message: '请输入模板名称!' },
            ]},
          mouldStatement:{
            rules: [
              { required: true, message: '请输入模板说明!'},
            ]},
          mouldUploadFile:{
            rules: [
              { required: true, message: '请输入选择模板!'},
            ]}
        }
      }
    },
    created () {

    },
    computed:{

    },
    methods: {
      handleCancel () {
        this.wordMouldModalVisible = false
      },
      handleSubmit () {
        this.form.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values);
            this.classificationModalVisible = false;
          }
        });
      },
      UploadHandleChange () {

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