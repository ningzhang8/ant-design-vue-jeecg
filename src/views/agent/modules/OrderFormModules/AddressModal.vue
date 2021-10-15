<template>
  <j-modal
    centered
    switchFullscreen
    :title="title"
    :width="1000"
    :visible="visible"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
  >
    <a-row>
      <a-col :md="8" :sm="24">
        <a-card title="分类" size="small">
          <div style="height: 300px;overflow-y:scroll;">
            <a-tree :load-data="onLoadData" :tree-data="treeData" @select="onSelectTree" :key="treeKey" />
          </div>
        </a-card>
      </a-col>
      <a-col :md="16" :sm="24">
        <a-card :bordered="false">
          <!-- 查询区域 -->
          <div class="table-page-search-wrapper">
            <a-form layout="inline">
              <a-row>
                <a-col :md="8" :sm="24">
                  <a-form-item label="过滤项目">
                    <a-select v-model="filteredProject">
                      <a-select-option v-for="item in filteredOptions" :key="item.key" :value="item.value">
                        {{ item.title }}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="8" :sm="24">
                  <a-form-item label="过滤值" style="margin-left: 18px">
                    <a-input v-model="filteredValue"></a-input>
                  </a-form-item>
                </a-col>
                <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
                  <a-col :md="8" :sm="24">
                    <a-button type="primary" icon="search" @click="searchQuery" style="margin-left: 18px"
                      >查询</a-button
                    >
                  </a-col>
                </span>
              </a-row>
            </a-form>
          </div>
          <!-- table区域-begin -->
          <div>
            <a-table
              :columns="columns"
              :dataSource="dataSource"
              :pagination="pagination"
              size="small"
              :rowKey="record => record.FCity"
              :rowSelection="{ selectedRowKeys: selectedRowKeys, onChange: onSelectChange, type: 'radio' }"
            />
          </div>
        </a-card>
      </a-col>
    </a-row>
    <!-- <a-tree-select
      v-model="value"
      show-search
      style="width: 100%"
      :dropdown-style="{ maxHeight: '400px', overflow: 'auto' }"
      :tree-data="treeData"
      @change="treeChange"
      placeholder="Please select"
      allow-clear
      tree-default-expand-all
    >
    </a-tree-select>
    <a-table 
      :columns="columns" 
      :data-source="data"
      :row-selection="rowSelection"
    >
    </a-table> -->
  </j-modal>
</template>

<script>
import { getAddressTreeDataList, queryAddressListBySelectValue } from '@/api/api'

const columns = [
  {
    title: '地区',
    dataIndex: 'FArea',
    key: 'FArea'
  },
  {
    title: '省份',
    dataIndex: 'FProvince',
    key: 'FProvince'
  },
  {
    title: '区城市',
    key: 'FRegion',
    dataIndex: 'FRegion'
  },
  {
    title: '市县',
    key: 'FCity',
    dataIndex: 'FCity'
  }
]

export default {
  data() {
    return {
      title: '选择地址',
      visible: false,
      treeData: [],
      filteredOptions: [
        { title: '省份', value: 'fprovince', key: 'fprovince' },
        { title: '区城市', value: 'fregion', key: 'fregion' },
        { title: '市县', value: 'fcity', key: 'fcity' }
      ],
      filteredProject: '',
      filteredValue: '',
      columns,
      dataSource: [],
      pagination: {
        defaultPageSize: 5,
        size: 'small'
      },
      selectedRowKeys: [],
      selectedResult: {},
      treeKey: 0
    }
  },
  methods: {
    // 初始化树数据
    loadData() {
      this.treeKey += 1
      getAddressTreeDataList().then(res => {
        if (res.success) {
          this.treeData = res.result
        }
      })
    },
    onLoadData(treeNode) {
      return new Promise(resolve => {
        if (treeNode.dataRef.children) {
          resolve()
          return
        }
        let params = { keyLen: treeNode.eventKey.length, value: treeNode.title }
        setTimeout(() => {
          getAddressTreeDataList(params).then(res => {
            if (res.success) {
              if (treeNode.eventKey.length == 7) {
                for (let i = 0; i < res.result.length; i++) {
                  res.result[i].isLeaf = true
                }
              }
              treeNode.dataRef.children = res.result
              this.treeData = [...this.treeData]
              resolve()
            }
          })
        }, 500)
      })
    },
    handleOk() {
      this.$emit('address', this.selectedResult)
      this.visible = false
    },
    handleCancel() {
      this.visible = false
    },
    // 查询
    searchQuery: function() {
      let filteredProject = this.filteredProject
      let filteredValue = this.filteredValue
      if (filteredProject) {
        let params = { selectValue: filteredProject, inputValue: filteredValue }
        queryAddressListBySelectValue(params).then(res => {
          if (res.success) {
            this.dataSource = res.result
          }
        })
      }
    },
    onSelectTree: function(selectedKeys, info) {
      const that = this
      const selectData = info.selectedNodes[0].data.props
      let params = { selectValue: selectData.value, inputValue: selectData.title }
      queryAddressListBySelectValue(params).then(res => {
        if (res.success) {
          if (selectData.value != 'fcity') {
            this.dataSource = res.result
          } else {
            that.selectedResult = res.result[0]
            that.handleOk()
          }
        }
      })
    },
    onSelectChange: function(selectedRowKeys, selectedRows) {
      this.selectedResult = selectedRows[0]
      this.selectedRowKeys = selectedRowKeys
    }
  }
}
</script>
