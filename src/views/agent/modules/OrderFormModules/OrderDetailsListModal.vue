<template>
  <a-card :bordered="false">
    <!--  订单表明细区域——start -->
    <div>
      <a-table
        size="small"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :loading="loading"
      >
      <!-- <img slot="imagePath" slot-scope="imagePath" :src="imagePath" /> -->
      </a-table>
    </div>
    <!--  订单表明细区域——end -->
  </a-card>
</template>

<script>
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import { querySalOdrdListByMainId } from '@/api/api'

export default {
  name: "OrderDetailsListModal",
  mixins: [JeecgListMixin],
  data() {
    return {
      description: '订购明细信息',
      // 表头
      columns: [
        {
          title: '产品',
          align: "center",
          dataIndex: 'fproductdesc',
          key: 'fproductdesc',
        },
        {
          title: '长(cm)',
          align: "center",
          dataIndex: 'len',
          key: 'len',
        },
        {
          title: '宽(cm)',
          align: "center",
          dataIndex: 'width',
          key: 'width',
        },
        {
          title: '数量',
          align: "center",
          dataIndex: 'quality',
          key: 'quality',
        },
        {
          title: '合计面积',
          align: "center",
          dataIndex: 'area',
          key: 'area',
        },
        {
          title: '价格',
          align: "center",
          dataIndex: 'price',
          key: 'price',
        },
        {
          title: '含税单价',
          align: "center",
          dataIndex: 'unitPrice',
          key: 'unitPrice',
        },
        {
          title: '价税合计',
          align: "center",
          dataIndex: 'total',
          key: 'total',
        },
        {
          title: '底色',
          align: "center",
          dataIndex: 'color',
          key: 'color',
        },
        {
          title: 'LOGO色',
          align: "center",
          dataIndex: 'logoColor',
          key: 'logoColor',
        },
        {
          title: '围边方式',
          align: "center",
          dataIndex: 'borderMode',
          key: 'borderMode',
        },
        // {
        //   title: '图片',
        //   align: "center",
        //   dataIndex: 'imagePath',
        //   key: 'imagePath',
        //   scopedSlots: { customRender: 'imagePath' },
        // },
        {
          title: '其它要求',
          align: "center",
          dataIndex: 'otherReq',
          key: 'otherReq',
        },
      ]
    }
  },
  methods: {
      loadData() {
        // var params = this.getQueryParams();
        // if (params.mainId) {
        //   querySalOdrdListByMainId({id: params.mainId}).then((res) => {
        //     if (res.success) {
        //       this.dataSource = res.result;
        //     } else {
        //       this.dataSource = null;
        //     }
        //   })
        // }
      },
      getOrderMain(orderId) {
        querySalOdrdListByMainId({id: orderId}).then((res) => {
          if (res.success) {
            this.dataSource = res.result;
          } else {
            this.dataSource = null;
          }
        })
      },
  },
}
</script>

<style scoped>
</style>