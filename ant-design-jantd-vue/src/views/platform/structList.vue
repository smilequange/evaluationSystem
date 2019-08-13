<template>
  <a-row :gutter="10">
    <a-col :md="8" :sm="24">
      <a-card :bordered="false">
        <div style="background: #fff;padding-left:16px;height: 100%; margin-top: 5px">
          <div>
            <span class="struct-classification-list-title">结构体分类</span>
            <a-icon class="struct-classification-list-add-icon" type="plus" @click="addStructClassification" />
          </div>
          <a-input-search @search="onSearch" style="width:100%;margin-top: 10px" placeholder="搜索"/>
          <div v-for="(item, index) in structListData" class="struct-classification-list-item" :key="index" :tabindex="index">
            <a-icon class="struct-classification-list-item-icon" type="file-text" />
            <span class="struct-classification-list-item-name">{{item.value}}</span>
            <a-icon class="struct-classification-list-item-delete" type="delete" @click="deleteStructClassification" />
            <a-icon class="struct-classification-list-item-edit" type="edit" @click="editStructClassification" />
          </div>
        </div>
      </a-card>
    </a-col>
    <a-col :md="16" :sm="24">
      <a-card :bordered="false">
        <div style="padding-bottom: 20px">
          <a-row :gutter="10">
            <a-col :md="8" :sm="8">
              <a-input placeholder="结构体名称" />
            </a-col>
            <a-col :md="8" :sm="8">
              <a-input placeholder="结构体备注" />
            </a-col>
            <a-col :md="2" :sm="2" :offset="3">
              <a-button>搜索</a-button>
            </a-col>
            <a-col :md="2" :sm="2">
              <a-button type="primary" @click="addStructInfo">新增</a-button>
            </a-col>
          </a-row>
        </div>
        <a-table
          :columns="structTableColumn"
          :dataSource="structTableListData"
          :loading="structTableLoading"
          :pagination="structTablePagination"
          rowKey="structNum"
        >
          <span slot="structOperation">
            <a @click="structTableCheck">查看</a>
            <a-divider type="vertical"/>
            <a @click="structTableEdit">编辑</a>
            <a-divider type="vertical"/>
            <a-popconfirm title="确定删除吗?" @confirm="() => {}">
              <a>删除</a>
            </a-popconfirm>
          </span>
        </a-table>
        <StructInfoModal ref="structInfoModalForm" />
      </a-card>
    </a-col>
    <a-modal
      :title="classificationModalTitle"
      :visible="classificationModalVisible"
      @ok="classificationModalOk"
      :confirmLoading="classificationModalLoading"
      @cancel="classificationModalCancel"
    >
      <a-form
        :form="form"
      >
        <a-form-item
          label="类名"
          :label-col="{ span: 5 }"
          :wrapper-col="{ span: 15 }"
        >
          <a-input
            v-decorator="['structClassificationName',{rules: [{ required: true, message: '请输入结构体分类名称!' }]}]"
          />
        </a-form-item>
      </a-form>
    </a-modal>
  </a-row>
</template>
<script>
//  import {JantdListMixin} from '@/mixins/JantdListMixin'
  import AFormItem from 'ant-design-vue/es/form/FormItem';
  import ARow from 'ant-design-vue/es/grid/Row';
  import StructInfoModal from './modules/StructInfoModal';

export default {
    name: 'DepartUserList',
//    mixins: [JantdListMixin],
    components: {
      ARow,
      AFormItem,
      StructInfoModal
    },
    data() {
      return {
        form: this.$form.createForm(this),
        structListData: [
          { key: '0', value: 'test1' },
          { key: '1', value: 'test12' },
          { key: '2', value: 'test123' }
        ],
        classificationModalTitle: '新增分类',
        classificationModalVisible: false,
        classificationModalLoading: false,
        structTableColumn: [
          {
            title: '序号',
            sorter:  (a, b) => a.structNum - b.structNum,
            align: 'center',
            dataIndex: 'structNum'
          },
          {
            title: '结构体名称',
            sorter: (a, b) => a.structName.length - b.structName.length,
            align: 'center',
            dataIndex: 'structName'
          },
          {
            title: '结构体备注',
            sorter: (a, b) => a.structRemark.length - b.structRemark.length,
            align: 'center',
            dataIndex: 'structRemark'
          },
          {
            title: '结构体操作',
            align: 'center',
            dataIndex: 'structOperation',
            scopedSlots: { customRender: 'structOperation' }
          }
        ],
        structTableListData: [
          {
            structNum: '1',
            structName: 'test',
            structRemark: 'no remark'
          },
          {
            structNum: '2',
            structName: 'test pro',
            structRemark: 'no remark pro'
          }
        ],
        structTableLoading: false,
        structTablePagination: {
          current: 1,
          pageSize: 10,
          pageSizeOptions: ['10', '20', '30'],
          showTotal: (total, range) => {
            return range[0] + '-' + range[1] + ' 共' + total + '条'
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        }
      }
    },
    methods: {
      onSearch () {

      },
      classificationModalOk () {
        this.form.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values);
            this.classificationModalVisible = false;
          }
        });
      },
      classificationModalCancel () {
        this.classificationModalVisible = false;
      },
      addStructClassification () {
        this.classificationModalVisible = true;
        this.classificationModalTitle = '新增分类';
        this.form.resetFields();
      },
      editStructClassification () {
        this.classificationModalVisible = true;
        this.classificationModalTitle = '编辑分类';
      },
      deleteStructClassification () {
        this.$confirm({
          title: '提示',
          content: '确定删除此分类（此操作不可逆）?',
          onOk() {
//            return new Promise((resolve, reject) => {
//              setTimeout(Math.random() > 0.5 ? resolve : reject, 1000);
//            }).catch(() => console.log('Oops errors!'));
          },
          onCancel() {},
        });
      },
      structTableCheck () {
        this.$refs.structInfoModalForm.structModalVisible = true;
        this.$refs.structInfoModalForm.title = '结构体信息';
        this.$refs.structInfoModalForm.disableSubmit= true;
      },
      structTableEdit () {
        this.$refs.structInfoModalForm.structModalVisible = true;
        this.$refs.structInfoModalForm.title = '编辑';
        this.$refs.structInfoModalForm.disableSubmit= false;
      },
      addStructInfo () {
        this.$refs.structInfoModalForm.structModalVisible = true;
        this.$refs.structInfoModalForm.title = '新增 ';
        this.$refs.structInfoModalForm.disableSubmit= false;
        this.$refs.structInfoModalForm.form.resetFields();
      }
    },
    created() {

    }
  }
</script>
<style scoped>
  .struct-classification-list-title{
    font-size: 18px;
    font-weight: bold;
    padding-right: 270px;
  }
  .struct-classification-list-add-icon{
    font-size: 18px;
    color: #2eabff;
  }
  .struct-classification-list-add-icon:hover{
    cursor:pointer;
  }
  .struct-classification-list-item-icon{
    font-size: 18px;
    padding-right: 10px;
  }
  .struct-classification-list-item-name{
    font-size: 18px;
  }
  .struct-classification-list-item-edit{
    font-size: 18px;
    float: right;
    margin: 5px 20px 0 0;
  }
  .struct-classification-list-item-edit:hover{
    cursor:pointer;
  }
  .struct-classification-list-item-delete{
    font-size: 18px;
    float: right;
    margin: 5px 10px 0 0;
  }
  .struct-classification-list-item-delete:hover{
    cursor:pointer;
  }
  .struct-classification-list-item{
    padding: 8px 0 8px 0;
  }
  .struct-classification-list-item:hover{
    background-color: #d9d9d9;
  }
  .struct-classification-list-item:focus{
    background-color: #2eabff;
  }
</style>