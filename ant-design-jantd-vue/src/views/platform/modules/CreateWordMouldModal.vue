<template>
  <a-modal
    title="选择模板"
    :visible="createWordMouldModalVisible"
    @ok="createWordMouldModalOk"
    :confirmLoading="createWordMouldModalLoading"
    @cancel="createWordMouldModalCancel"
    :width="1400"
  >
    <a-row :gutter="10">
      <a-col :md="8" :sm="24">
        <a-card :bordered="false">
          <div style="background: #fff;padding-left:16px;height: 100%; margin-top: 5px">
            <div>
              <span class="mould-classification-list-title">指标分类</span>
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
                <a-button type="primary">选择指标</a-button>
              </a-col>
            </a-row>
          </div>
          <a-table
            :columns="wordMouldTableColumn"
            :dataSource="wordMouldTableData"
            :loading="wordMouldTableLoading"
            :pagination="wordMouldTablePagination"
            rowKey="mouldIndexName"
          >
          <span slot="wordMouldOperation">
            <a>编辑</a>
            <a-divider type="vertical"/>
            <a-popconfirm title="确定删除吗?" @confirm="() => {}">
              <a>删除</a>
            </a-popconfirm>
          </span>
          </a-table>
        </a-card>
      </a-col>
    </a-row>
  </a-modal>
</template>
<script>
import ATree from "ant-design-vue/es/tree/Tree";

export default {
    name: 'CreateWordMouldModal',
//    mixins: [JantdListMixin],
    components: {
      ATree
    },
    data() {
      return {
        expandedKeys: [],
        searchValue: '',
        autoExpandParent: true,
        mouldTreeData: [
          {
            key: '0-0',
            title: '指标分类',
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
        wordMouldTableColumn: [
          {
            title: '指标名称',
            sorter: (a, b) => a.mouldIndexName.length - b.mouldIndexName.length,
            align: 'center',
            dataIndex: 'mouldIndexName'
          },
          {
            title: '测试状态',
            sorter: (a, b) => a.mouldIndexTestStatus.length - b.mouldIndexTestStatus.length,
            align: 'center',
            dataIndex: 'mouldIndexTestStatus'
          },
          {
            title: '指标内容',
            sorter: (a, b) => a.mouldIndexContent.length - b.mouldIndexContent.length,
            align: 'center',
            dataIndex: 'mouldIndexContent'
          },
          {
            title: '更新时间',
            sorter: (a, b) => a.wordMouldStatement.length - b.wordMouldStatement.length,
            align: 'center',
            dataIndex: 'mouldIndexUpdateTime'
          }
        ],
        wordMouldTableData: [
          {
            mouldIndexName: 'test',
            mouldIndexTestStatus: 'none',
            mouldIndexUpdateTime: '2019-01-01 12:00',
            mouldIndexContent: 'no statement'
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
        },
        createWordMouldModalVisible: false,
        createWordMouldModalLoading: false
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
      createWordMouldModalOk () {
        this.createWordMouldModalVisible = false
      },
      createWordMouldModalCancel () {
        this.createWordMouldModalVisible = false
      }
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
</style>