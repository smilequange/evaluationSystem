<template>
  <a-row :gutter="10">
    <a-col :md="8" :sm="24">
      <a-card :bordered="false">
        <div style="background: #fff;padding-left:16px;height: 100%; margin-top: 5px">
          <div>
            <span class="mould-classification-list-title">模板分类</span>
            <a-icon class="mould-classification-list-copy-icon" type="copy" @click="copyWordMould"/>
            <a-icon class="mould-classification-list-plus-icon" type="plus" @click="addWordMould"/>
            <a-icon class="mould-classification-list-form-icon" type="form" @click="editWordMould"/>
            <a-icon class="mould-classification-list-delete-icon" type="delete" @click="deleteWordMould"/>
          </div>
          <a-input-search @change="onChange" style="width:100%;margin-top: 10px" placeholder="搜索"/>
          <a-tree
            @expand="onExpand"
            :expandedKeys="expandedKeys"
            :autoExpandParent="autoExpandParent"
            :treeData="mouldTreeData"
          >
            <template slot="title" slot-scope="{title}">
              <span v-if="title.indexOf(searchValue) > -1">
                {{title.substr(0, title.indexOf(searchValue))}}
                <span style="color: #f50">{{searchValue}}</span>
                {{title.substr(title.indexOf(searchValue) + searchValue.length)}}
              </span>
              <span v-else>{{title}}</span>
            </template>
          </a-tree>
        </div>
      </a-card>
    </a-col>
    <a-col :md="16" :sm="24">
      <a-card :bordered="false">
        <div style="padding-bottom: 20px">
          <a-row :gutter="10">
            <a-col :md="8" :sm="8">
              <a-input placeholder="模板名称" />
            </a-col>
            <a-col :md="2" :sm="2">
              <a-button>搜索</a-button>
            </a-col>
            <a-col :md="2" :sm="2">
              <a-button type="primary" @click="upLoadWordMould">上传模板</a-button>
            </a-col>
          </a-row>
        </div>
        <a-table
          :columns="wordMouldTableColumn"
          :dataSource="wordMouldTableData"
          :loading="wordMouldTableLoading"
          :pagination="wordMouldTablePagination"
          rowKey="wordMouldNum"
        >
          <span slot="wordMouldOperation">
            <a @click="editWordMouldInfo">编辑</a>
            <a-divider type="vertical"/>
            <a-popconfirm title="确定删除吗?" @confirm="() => {}">
              <a>删除</a>
            </a-popconfirm>
          </span>
        </a-table>
        <WordMouldInfoModal ref="wordMouldInfoModalForm" />
      </a-card>
    </a-col>
    <CreateWordMouldModal ref="createWordMouldModalForm" />
    <a-modal
      :title="wordMouldModalTitle"
      :visible="wordMouldModalVisible"
      @ok="wordMouldModalOk"
      :confirmLoading="wordMouldModalLoading"
      @cancel="wordMouldModalCancel"
    >
      <a-form
        :form="form"
      >
        <a-form-item
          label="名称"
          :label-col="{ span: 5 }"
          :wrapper-col="{ span: 15 }"
        >
          <a-input
            v-decorator="['wordMouldName',{rules: [{ required: true, message: '请输入模板分类名称!' }]}]"
          />
        </a-form-item>
      </a-form>
    </a-modal>
  </a-row>
</template>
<script>
//  import {JantdListMixin} from '@/mixins/JantdListMixin'
//  import AFormItem from 'ant-design-vue/es/form/FormItem';
//  import ARow from 'ant-design-vue/es/grid/Row';
//  import StructInfoModal from './modules/StructInfoModal';

import ATree from "ant-design-vue/es/tree/Tree";
import CreateWordMouldModal from './modules/CreateWordMouldModal';
import WordMouldInfoModal from './modules/WordMouldInfoModal';
export default {
    name: 'WordMouldList',
//    mixins: [JantdListMixin],
    components: {
      ATree,
      CreateWordMouldModal,
      WordMouldInfoModal
//      ARow,
//      AFormItem,
//      StructInfoModal
    },
    data() {
      return {
        expandedKeys: [],
        searchValue: '',
        autoExpandParent: true,
        mouldTreeData: [
          {
            key: '0-0',
            title: '模板分类',
            scopedSlots: { title: 'title' },
            children: [
              {
                key: '0-0-1',
                title: '测试1',
                scopedSlots: { title: 'title' }
              },
              {
                key: '0-0-2',
                title: '测试2',
                scopedSlots: { title: 'title' }
              }
            ]
          }
        ],
        dataList: [],
        wordMouldModalTitle: '新增分类',
        wordMouldModalVisible: false,
        wordMouldModalLoading: false,
        form: this.$form.createForm(this),
        wordMouldTableColumn: [
          {
            title: '序号',
            sorter:  (a, b) => a.wordMouldNum - b.wordMouldNum,
            align: 'center',
            dataIndex: 'wordMouldNum'
          },
          {
            title: '模板名称',
            sorter: (a, b) => a.wordMouldName.length - b.wordMouldName.length,
            align: 'center',
            dataIndex: 'wordMouldName'
          },
          {
            title: '上传时间',
            sorter: (a, b) => a.wordMouldUploadTime.length - b.wordMouldUploadTime.length,
            align: 'center',
            dataIndex: 'wordMouldUploadTime'
          },
          {
            title: '更新时间',
            sorter: (a, b) => a.wordMouldUpdateTime.length - b.wordMouldUpdateTime.length,
            align: 'center',
            dataIndex: 'wordMouldUpdateTime'
          },
          {
            title: '模板说明',
            sorter: (a, b) => a.wordMouldStatement.length - b.wordMouldStatement.length,
            align: 'center',
            dataIndex: 'wordMouldStatement'
          },
          {
            title: '指标操作',
            align: 'center',
            dataIndex: 'wordMouldOperation',
            scopedSlots: { customRender: 'wordMouldOperation' }
          }
        ],
        wordMouldTableData: [
          {
            wordMouldNum: '1',
            wordMouldName: 'test',
            wordMouldUploadTime: '2019-01-01 12:00',
            wordMouldUpdateTime: '2019-01-01 12:00',
            wordMouldStatement: 'no statement'
          }
        ],
        wordMouldTableLoading: false,
        wordMouldTablePagination: {
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
      onChange (e) {
        const value = e.target.value
        const expandedKeys = this.dataList.map((item) => {
          if (item.title.indexOf(value) > -1) {
            return this.getParentKey(item.key, this.mouldTreeData)
          }
          return null
        }).filter((item, i, self) => item && self.indexOf(item) === i)
        Object.assign(this, {
          expandedKeys,
          searchValue: value,
          autoExpandParent: true,
        })
      },
      onExpand (expandedKeys) {
        this.expandedKeys = expandedKeys
        this.autoExpandParent = false
      },
      getGenerateList (data) {
        for (let i = 0; i < data.length; i++) {
          const node = data[i]
          const key = node.key
          const title = node.title
          this.dataList.push({ key, title })
          if (node.children) {
            this.getGenerateList(node.children)
          }
        }
      },
      getParentKey (key, tree) {
        let parentKey
        for (let i = 0; i < tree.length; i++) {
          const node = tree[i]
          if (node.children) {
            if (node.children.some(item => item.key === key)) {
              parentKey = node.key
            } else if (this.getParentKey(key, node.children)) {
              parentKey = this.getParentKey(key, node.children)
            }
          }
        }
        return parentKey
      },
      wordMouldModalOk () {
        this.form.validateFields((err, values) => {
          if (!err) {
            console.log('Received values of form: ', values)
            this.wordMouldModalVisible = false
          }
        });
      },
      wordMouldModalCancel () {
        this.wordMouldModalVisible = false
      },
      addWordMould () {
        this.wordMouldModalVisible = true
        this.wordMouldModalTitle = '新增分类'
      },
      editWordMould () {
        this.wordMouldModalVisible = true
        this.wordMouldModalTitle = '编辑分类'
      },
      deleteWordMould () {
        this.$confirm({
          title: '提示',
          content: '确定删除此分类（此操作不可逆）?',
          onOk() {
            return new Promise((resolve, reject) => {
              setTimeout(Math.random() > 0.5 ? resolve : reject, 1000);
            }).catch(() => console.log('Oops errors!'));
          },
          onCancel() {},
        });
      },
      copyWordMould () {
        this.$refs.createWordMouldModalForm.createWordMouldModalVisible = true;
      },
      editWordMouldInfo () {
        this.$refs.wordMouldInfoModalForm.wordMouldModalVisible = true;
      },
      upLoadWordMould () {
        this.$refs.wordMouldInfoModalForm.wordMouldModalVisible = true;
      }
//      addStructClassification () {
//        this.classificationModalVisible = true;
//        this.classificationModalTitle = '新增分类';
//      },
//      editStructClassification () {
//        this.classificationModalVisible = true;
//        this.classificationModalTitle = '编辑分类';
//      },
//      deleteStructClassification () {
//        this.$confirm({
//          title: '提示',
//          content: '确定删除此分类（此操作不可逆）?',
//          onOk() {
//            return new Promise((resolve, reject) => {
//              setTimeout(Math.random() > 0.5 ? resolve : reject, 1000);
//            }).catch(() => console.log('Oops errors!'));
//          },
//          onCancel() {},
//        });
//      },
//      structTableCheck () {
//        this.$refs.structInfoModalForm.structModalVisible = true;
//        this.$refs.structInfoModalForm.title = '结构体信息';
//        this.$refs.structInfoModalForm.disableSubmit= true;
//      },
//      structTableEdit () {
//        this.$refs.structInfoModalForm.structModalVisible = true;
//        this.$refs.structInfoModalForm.title = '编辑';
//        this.$refs.structInfoModalForm.disableSubmit= false;
//      },
//      addStructInfo () {
//        this.$refs.structInfoModalForm.structModalVisible = true;
//        this.$refs.structInfoModalForm.title = '新增 ';
//        this.$refs.structInfoModalForm.disableSubmit= false;
//      }
    },
    mounted () {
      this.dataList = []
      this.getGenerateList(this.mouldTreeData)
    },
    created () {

    }
  }
</script>
<style scoped>
  .mould-classification-list-title{
    font-size: 18px;
    font-weight: bold;
    padding-right: 200px;
  }
  .mould-classification-list-copy-icon{
    font-size: 18px;
    color: #2eabff;
    margin-right: 10px;
  }
  .mould-classification-list-copy-icon:hover{
    cursor:pointer;
  }
  .mould-classification-list-plus-icon{
    font-size: 18px;
    color: #2eabff;
    margin-right: 10px;
  }
  .mould-classification-list-plus-icon:hover{
    cursor:pointer;
  }
  .mould-classification-list-form-icon{
    font-size: 18px;
    color: #2eabff;
    margin-right: 10px;
  }
  .mould-classification-list-form-icon:hover{
    cursor:pointer;
  }
  .mould-classification-list-delete-icon{
    font-size: 18px;
    color: #2eabff;
  }
  .mould-classification-list-delete-icon:hover{
    cursor:pointer;
  }
</style>