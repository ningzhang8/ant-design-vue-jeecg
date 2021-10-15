<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="7" :sm="24">
            <a-form-item label="订货日期">
              <a-range-picker v-model="queryParam.orderDate" @change="onOrderDateChange" />
            </a-form-item>
          </a-col>
          <a-col :md="7" :sm="24">
            <a-form-item label="要求出货日">
              <a-range-picker v-model="queryParam.sellDate" @change="onSellDateChange" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="24">
          <a-col :md="7" :sm="24">
            <a-form-item label="订单编号">
              <a-input placeholder="请输入订单编号" v-model="queryParam.orderNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="7" :sm="24">
            <a-form-item label="客户订单号">
              <a-input placeholder="请输入客户订单号" v-model="queryParam.customerCodeNumber"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="24">
            <a-form-item label="代理商简称">
              <a-input v-model="queryParam.FAgentName" :disabled="true"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="5" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete" />
            删除
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作
          <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>

    <!-- 主表区域-begin  -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i>
        <span>已选择</span>
        <a style="font-weight: 600">
          {{ selectedRowKeys.length }}
        </a>
        <span>项</span>
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="small"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{
          selectedRowKeys: selectedRowKeys,
          onChange: onSelectChange,
          type: type
        }"
        @change="handleTableChange"
        :customRow="clickThenCheck"
      >
        <span slot="action" slot-scope="text, record">
          <template v-if="record.frecstat != '1'">
            <a @click="handleEdit(record)">编辑</a>
            <a-divider type="vertical" />
            <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
              <a>删除</a>
            </a-popconfirm>
          </template>
          <template v-else>
            <a href="javascript:;" @click="handleDetail(record)">详情</a>
          </template>
        </span>
      </a-table>
    </div>
    <!-- 主表区域-end -->

    <!-- 明细区域 -->
    <a-tabs defaultActiveKey="1">
      <a-tab-pane tab="订购明细" key="1">
        <order-details-list-modal ref="OrderDetailsListModal"></order-details-list-modal>
      </a-tab-pane>
    </a-tabs>

    <!-- 编辑区域 -->
    <order-main-modal ref="modalForm" @ok="modalFormOk"></order-main-modal>
  </a-card>
</template>

<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import { getAgentByLoginUser, deleteOrderForm } from '@/api/api'
import { getAction } from '@/api/manage'
import OrderMainModal from './modules/OrderFormModules/OrderMainModal'
import OrderDetailsListModal from './modules/OrderFormModules/OrderDetailsListModal'

export default {
  name: 'OrderForm',
  mixins: [JeecgListMixin],
  components: {
    OrderMainModal,
    OrderDetailsListModal
  },
  data() {
    return {
      description: '订单档案页面',
      type: 'radio',
      /* 分页参数 */
      ipagination: {
        current: 1,
        pageSize: 5,
        pageSizeOptions: ['5', '10', '20'],
        showTotal: (total, range) => {
          return range[0] + '-' + range[1] + ' 共' + total + '条'
        },
        showQuickJumper: true,
        showSizeChanger: true,
        total: 0
      },
      // 请求参数
      url: {
        list: '/orderform/salOdrm/list'
      },
      defaultData: [],
      // 表头
      columns: [
        {
          title: '#',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '订货日期',
          align: 'center',
          dataIndex: 'orderDate'
        },
        {
          title: '要求出货日',
          align: 'center',
          dataIndex: 'sellDate'
        },
        {
          title: '订单编号',
          align: 'center',
          dataIndex: 'orderNumber'
        },
        {
          title: '客户订单号',
          align: 'center',
          dataIndex: 'customerOrderNumber'
        },
        {
          title: '创建人',
          align: 'center',
          dataIndex: 'FUserName'
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          scopedSlots: { customRender: 'action' }
        }
      ]
    }
  },
  created() {
    getAgentByLoginUser().then(res => {
        if (res.success) {
          this.$set(this.queryParam, 'FAgentName', res.result)
        }
      })
  },
  methods: {
    loadData(arg) {
      if(!this.url.list){
        this.$message.error("请设置url.list属性!")
        return
      }
      //加载数据 若传入参数1则加载第一页的内容
      if (arg === 1) {
        this.ipagination.current = 1;
      }
      var params = this.getQueryParams();//查询条件
      delete params.orderDate; // 订货日期参数不传递后台
      delete params.sellDate; // 要求出货日参数不传递后台
      this.loading = true;
      getAction(this.url.list, params).then((res) => {
        if (res.success) {
          this.dataSource = res.result.records||res.result;

          if(res.result.total)
          {
            this.ipagination.total = res.result.total;
          }else{
            this.ipagination.total = 0;
          }
        }
        if(res.code===510){
          this.$message.warning(res.message)
        }
        this.loading = false;
      })
      
    },
    // 点击主表行呈现订单明细
    clickThenCheck(record) {
      return {
        on: {
          click: () => {
            this.onSelectChange(record.id.split(','), [record])
          }
        }
      }
    },
    // 主表行 onChange
    onSelectChange(selectedRowKeys, selectionRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectionRows = selectionRows
      this.$refs.OrderDetailsListModal.getOrderMain(this.selectedRowKeys[0])
    },
    // 清空
    onClearSelected() {
      this.selectedRowKeys = []
      this.selectionRows = []
      this.$refs.OrderDetailsListModal.queryParam.mainId = null
      this.$refs.OrderDetailsListModal.loadData()
      this.$refs.OrderDetailsListModal.selectedRowKeys = []
      this.$refs.OrderDetailsListModal.selectionRows = []
    },
    // 删除
    handleDelete: function(id) {
      var that = this
      deleteOrderForm({ id: id }).then(res => {
        if (res.success) {
          that.$message.success(res.message)
          that.loadData()
          this.$refs.OrderDetailsListModal.loadData()
        } else {
          that.$message.warning(res.message)
        }
      })
    },
    // 查询
    searchQuery: function() {
      this.selectedRowKeys = []
      this.selectionRows = []
      this.$refs.OrderDetailsListModal.queryParam.mainId = null
      this.$refs.OrderDetailsListModal.loadData()
      this.$refs.OrderDetailsListModal.selectedRowKeys = []
      this.$refs.OrderDetailsListModal.selectionRows = []
      this.loadData()
    },
    onOrderDateChange: function (value, dateString) {
      this.queryParam.orderDate_begin=dateString[0];
      this.queryParam.orderDate_end=dateString[1];
    },
    onSellDateChange: function (value, dateString) {
      this.queryParam.sellDate_begin=dateString[0];
      this.queryParam.sellDate_end=dateString[1];
    }
  }
}
</script>

<style scoped></style>
