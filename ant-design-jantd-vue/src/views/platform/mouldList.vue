<template>
  <a-row :gutter="10">
    <a-col :md="8" :sm="24">
      <a-card :bordered="false">
        <div style="background: #fff;padding-left:16px;height: 100%; margin-top: 5px">
          <div>
            <span class="mould-classification-list-title">模板分类</span>
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
            <a-col :md="8" :sm="8">
              <a-input placeholder="模板内容" />
            </a-col>
            <a-col :md="8" :sm="2">
              <a-button>搜索</a-button>
            </a-col>
          </a-row>
        </div>
        <a-table
          :columns="mouldTableColumn"
          :dataSource="mouldTableData"
          :loading="mouldTableLoading"
          :pagination="mouldTablePagination"
          rowKey="mouldNum"
        >
          <span slot="mouldOperation">
            <a-popconfirm title="确定删除吗?" @confirm="() => {}">
              <a>删除</a>
            </a-popconfirm>
          </span>
        </a-table>
      </a-card>
    </a-col>
  </a-row>
</template>
<script>
//  import {JantdListMixin} from '@/mixins/JantdListMixin'
import ATree from "ant-design-vue/es/tree/Tree";
export default {
    name: 'MouldList',
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
        mouldTableColumn: [
          {
            title: '序号',
            sorter:  (a, b) => a.mouldNum - b.mouldNum,
            align: 'center',
            dataIndex: 'mouldNum'
          },
          {
            title: '模板名称',
            sorter: (a, b) => a.mouldName.length - b.mouldName.length,
            align: 'center',
            dataIndex: 'mouldName'
          },
          {
            title: '模板内容',
            sorter: (a, b) => a.mouldContent.length - b.mouldContent.length,
            align: 'center',
            dataIndex: 'mouldContent'
          },
          {
            title: '更新时间',
            sorter: (a, b) => a.mouldUpdateTime.length - b.mouldUpdateTime.length,
            align: 'center',
            dataIndex: 'mouldUpdateTime'
          },
          {
            title: '指标操作',
            align: 'center',
            dataIndex: 'mouldOperation',
            scopedSlots: { customRender: 'mouldOperation' }
          }
        ],
        mouldTableData: [
          {
            mouldNum: '1',
            mouldName: 'test',
            mouldContent: 'none',
            mouldUpdateTime: '2019-01-01 12:00'
          }
        ],
        mouldTableLoading: false,
        mouldTablePagination: {
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