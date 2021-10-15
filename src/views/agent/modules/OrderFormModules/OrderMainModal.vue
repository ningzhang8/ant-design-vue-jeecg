<template>
  <j-modal
    title="订单档案"
    switchFullscreen
    :width="1200"
    :visible="visible"
    :destroyOnClose="true"
    :maskClosable="false"
    :confirmLoading="confirmLoading"
    :okButtonProps="{ props: { disabled: disableSubmit } }"
    @ok="handleSubmit"
    @cancel="handleCancel"
  >
    <a-spin :spinning="confirmLoading">
      <a-form-model ref="form" :model="model" :labelCol="labelCol" :wrapperCol="wrapperCol">
        <!-- 主表单区域 -->
        <a-row class="form-row">
          <a-col :span="18">
            <a-col :span="6">
              <a-form-model-item label="订货日期">
                <a-date-picker :size="size" :style="inputStyle" v-model="model.orderDate"  @change="onOrderDateChange" />
              </a-form-model-item>
            </a-col>
            <a-col :span="6">
              <a-form-model-item label="要求出货日">
                <a-date-picker :size="size" :style="inputStyle" v-model="model.sellDate" />
              </a-form-model-item>
            </a-col>
            <!-- <a-col :lg="8">
              <a-form-model-item
                align="right"
              >
                <vue-qr
                  :correctLevel="3"
                  :autoColor="false"
                  colorDark="#313a90"
                  :text="codeUrl"
                  :size="60"
                  :margin="0"
                  :logoMargin="3"
                />
              </a-form-model-item>
            </a-col> -->
          </a-col>
          <a-col :span="6">
            <a-form-model-item label="订单编号">
              <a-input v-model="model.orderNumber" :size="size" :disabled="true" />
            </a-form-model-item>
          </a-col>
        </a-row>

        <a-row class="form-row">
          <a-col :span="18">
            <a-row class="form-row">
              <a-col :lg="6">
                <a-form-model-item label="代理商简称">
                  <a-input v-model="model.FAgentName" :size="size" :style="inputStyle" :disabled="true" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="业务员">
                  <a-input v-model="model.salesman" :size="size" :style="inputStyle" :disabled="true" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="币种">
                  <a-input v-model="model.fcurrency" :size="size" :style="inputStyle" :disabled="true" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="订单来源">
                  <a-input v-model="model.orderSource" :size="size" :style="inputStyle" />
                </a-form-model-item>
              </a-col>
            </a-row>

            <a-row class="form-row">
              <a-col :lg="6">
                <a-form-model-item label="详细地址">
                  <a-input v-model="model.fullAddress" :size="size" style="width: 328px;" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6"></a-col>
              <a-col :lg="6">
                <a-form-model-item label="收货人">
                  <a-input v-model="model.cnee" :size="size" :style="inputStyle" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="电话">
                  <a-input v-model="model.tel" :size="size" :style="inputStyle" />
                </a-form-model-item>
              </a-col>
            </a-row>

            <a-row class="form-row">
              <a-col :lg="6">
                <a-form-model-item label="市">
                  <a-input v-model="model.region" @click="handleChangeForCity" :size="size" :style="inputStyle" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="地区">
                  <a-input v-model="model.city" :size="size" :style="inputStyle" :disabled="true" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="省份">
                  <a-input v-model="model.province" :size="size" :style="inputStyle" :disabled="true" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6">
                <a-form-model-item label="客户订单号" v-model="model.customer_order_number">
                  <a-input v-model="model.customerOrderNumber" :size="size" :style="inputStyle" />
                </a-form-model-item>
              </a-col>
            </a-row>

            <a-row class="form-row">
              <a-col :lg="6">
                <a-form-model-item label="市区省">
                  <a-input v-model="model.address" :size="size" style="width: 328px;" :disabled="true" />
                </a-form-model-item>
              </a-col>
              <a-col :lg="6"> </a-col>
              <a-col :lg="6">
                <a-form-model-item label="快递公司">
                  <a-select :size="size" :style="inputStyle" v-model="model.fkexpressid">
                    <a-select-option v-for="item in selectItems" :value="item.value" :key="item.value">
                      {{ item.name }}
                    </a-select-option>
                  </a-select>
                </a-form-model-item>
              </a-col>
              <a-col :lg="6"> </a-col>
            </a-row>
          </a-col>

          <a-col :span="6">
            <a-form-model-item label="原始订单信息" :wrapper-col="{ lg: 24 }">
              <a-textarea
                placeholder="请输入原始订单信息"
                v-model="model.orderInfo"
                :auto-size="{ minRows: 4, maxRows: 4 }"
                @change="informationOnChange"
                :size="size"
              >
              </a-textarea>
            </a-form-model-item>
          </a-col>
        </a-row>

        <a-row class="form-row">
          <a-col :lg="18">
            <a-col :lg="6">
              <a-form-model-item label="发货要求">
                <a-input v-model="model.req" :size="size" style="width: 328px;" />
              </a-form-model-item>
            </a-col>
            <a-col :lg="6"> </a-col>
            <a-col :lg="6">
              <a-form-model-item label="附档一">
                <j-upload v-model="model.fileList1" text="上传" :multiple="false"></j-upload>
              </a-form-model-item>
            </a-col>
            <a-col :lg="6">
            <a-form-model-item label="附档二">
              <j-upload v-model="model.fileList2" text="上传" :multiple="false"></j-upload>
            </a-form-model-item>
          </a-col>
          </a-col>
          <a-col :lg="6">
            <a-form-model-item label="创建人">
              <a-input v-model="model.FUserName" :size="size" :disabled="true" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>

      <!-- 子表单区域 -->
      <!-- 订单明细区域 -->
      <a-card :bordered="false" title="订单明细">
        <j-editable-table
          ref="editabletable"
          size="small"
          :maxHeight="300"
          :loading="table.loading"
          :dataSource="table.dataSource"
          :columns="table.columns"
          :actionButton="true"
          :rowSelection="true"
          @valueChange="handleValueChange"
        />
      </a-card>
    </a-spin>

    <!--  市地区省弹出框 --->
    <Address-modal ref="addressModal" @address="getAddress"></Address-modal>
  </j-modal>
</template>

<script>
import smart from 'address-smart-parse'
import moment from 'moment'
// import VueQr from 'vue-qr'
import JEditableTable from '@/components/jeecg/JEditableTable'
import {
  addOrderForm,
  editOrderForm,
  querySalOdrdListByMainId,
  getBorderModeList,
  getExpressList,
  getAgentUserByLoginUser,
  getPriceByPriceIdAndOrderDate
} from '@/api/api'
import { FormTypes, VALIDATE_NO_PASSED, getRefPromise, validateFormModelAndTables } from '@/utils/JEditableTableUtil'
import AddressModal from './AddressModal'

export default {
  name: 'OrderMainModal',
  components: {
    // VueQr,
    JEditableTable,
    AddressModal
  },
  data() {
    return {
      visible: false,
      confirmLoading: false,
      disableSubmit: false,
      model: {},
      labelCol: {
        xs: { span: 12 },
        sm: { span: 9 }
      },
      wrapperCol: {
        xs: { span: 12 },
        sm: { span: 24 - 13 }
      },
      size: 'small',
      inputStyle: {
        width: '112px'
      },
      selectItems: [],
      // logoSrc: require('../assets/img/qrlogo.png'),
      // bgSrc: require('../assets/img/bgSrc.png'),
      //codeUrl: 'https://www.yoojia.com/',
      // 客户信息
      // 请求参数
      url: {
        list: '/agent/vieRSBorderMode/list'
      },
      table: {
        loading: false,
        dataSource: [],
        columns: [
          {
            title: '产品',
            key: 'fproductdesc',
            type: FormTypes.popup,
            width: '10%',
            popupCode: 'Vie_RS_Product',
            field: 'fproductdesc',
            orgFields: 'fproductdesc,fcategory,fsubcategory,fproductcode,fsubclacode,fpriceid',
            destFields: 'fproductdesc,fcategory,fsubcategory,fproductcode,fsubclacode,fpriceid'
          },
          {
            title: '长（cm)',
            key: 'len',
            width: '10%',
            type: FormTypes.inputNumber,
            defaultValue: ''
          },
          {
            title: '宽(cm)',
            key: 'width',
            width: '10%',
            type: FormTypes.inputNumber,
            defaultValue: ''
          },
          {
            title: '数量',
            key: 'quality',
            width: '10%',
            type: FormTypes.inputNumber,
            defaultValue: ''
          },
          {
            title: '合计面积',
            key: 'area',
            width: '10%',
            type: FormTypes.inputNumber,
            defaultValue: ''
          },
          {
            title: '价格',
            key: 'price',
            width: '10%',
            type: FormTypes.input,
            defaultValue: ''
          },
          {
            title: '含税单价',
            key: 'unitPrice',
            width: '10%',
            type: FormTypes.input,
            defaultValue: ''
          },
          {
            title: '价税合计',
            key: 'total',
            width: '10%',
            type: FormTypes.input,
            defaultValue: ''
          },
          {
            title: '底色',
            key: 'color',
            type: FormTypes.popup,
            width: '10%',
            popupCode: 'Vie_RS_ClaColor',
            field: 'fcolordesc',
            orgFields: 'fcolordesc',
            destFields: 'color'
          },
          {
            title: 'LOGO色',
            key: 'logoColor',
            width: '10%',
            type: FormTypes.popup,
            popupCode: 'Vie_RS_ClaColor',
            field: 'fcolordesc',
            orgFields: 'fcolordesc',
            destFields: 'logoColor'
          },
          {
            title: '围边方式',
            key: 'borderMode',
            width: '15%',
            type: FormTypes.select,
            // 下拉选项
            options: [],
            placeholder: '请选择'
          },
          {
            title: '图片',
            key: 'imagePath',
            type: FormTypes.image,
            width: '10%',
            token: true
          },
          {
            title: '其它要求',
            key: 'otherReq',
            width: '10%',
            type: FormTypes.input,
            defaultValue: ''
          },
          {
            key: 'fcategory',
            type: FormTypes.hidden
          },
          {
            key: 'fsubcategory',
            type: FormTypes.hidden
          },
          {
            key: 'fproductcode',
            type: FormTypes.hidden
          },
          {
            key: 'fsubclacode',
            type: FormTypes.hidden
          },
          {
            key: 'fpriceid',
            type: FormTypes.hidden
          },
        ]
      },
      fileList1: [],
      fileList2: [],
    }
  },
  mounted() {
    // 初始化订单明细——围边数据
    getBorderModeList().then(res => {
      if (res.success) {
        this.table.columns[10].options = res.result
      }
    })
    // 初始化订单——快递数据 selectItems
    getExpressList().then(res => {
      if (res.success) {
        this.selectItems = res.result
      }
    })
  },
  methods: {
    moment,
    // 初始化页面
    loadData() {
      // 自动生成订单编号
      this.getOrderNumber()
      // 系統登錄帳號關聯获取代理商简称，业务员，币种
      getAgentUserByLoginUser().then(res => {
        if (res.success) {
          this.$set(this.model, 'fkagentid', res.result.FKAgentID)
          this.$set(this.model, 'FAgentName', res.result.FAgentName)
          this.$set(this.model, 'salesman', res.result.FSalesman)
          this.$set(this.model, 'fcurrency', res.result.FCurrency)
          this.$set(this.model, 'FUserName', res.result.FSalesman)
        }
      })
      // 订货日期日期默认系统日期
      this.$set(this.model, 'orderDate', moment(new Date()))
    },
    // 获取所有的editableTable实例
    getAllTable() {
      return Promise.all([getRefPromise(this, 'editabletable')])
    },
    add() {
      // 默认新增一条数据
      this.getAllTable().then(editableTables => {
        editableTables[0].add()
      })
      this.table.dataSource = []
      this.edit({})
      this.loadData()
    },
    edit(record, v) {
      this.visible = true
      this.model = Object.assign({}, record)
      Object.keys(record).forEach(colKey => {
        if (colKey == 'orderDate' || colKey == 'sellDate') {
          this.$set(this.model, colKey, moment(this.model[colKey]));
        } else {
          this.$set(this.model, colKey, this.model[colKey])
        }
      })
      // 市区省
      const province = this.model.province != undefined ? this.model.province : ''
      const city = this.model.city != undefined ? this.model.city : ''
      const district = this.model.region != undefined ? this.model.region : ''
      this.$set(this.model, 'address', province + ' ' + city + ' ' + district)

      // 加载订购明细资料
      if (this.model.id) {
        let params = { id: this.model.id }
        querySalOdrdListByMainId(params)
          .then(res => {
            this.table.dataSource = res.result || []
          })
          .finally(() => {
            this.table.loading = false
          })
      }
    },
    close() {
      this.visible = false
      this.getAllTable().then(editableTables => {
        editableTables[0].initialize()
      })
      this.$emit('close')
      this.$refs.form.resetFields()
    },

    /** 触发表单验证 */
    validateFields() {
      this.getAllTable()
        .then(tables => {
          /** 一次性验证主表和所有的次表 */
          return validateFormModelAndTables(this.$refs.form, this.model, tables)
        })
        .then(allValues => {
          let formData = this.classifyIntoFormData(allValues)
          // 发起请求
          return this.requestAddOrEdit(formData)
        })
        .catch(e => {
          if (e.error === VALIDATE_NO_PASSED) {
            // 如果有未通过表单验证的子表，就自动跳转到它所在的tab
            //this.activeKey = e.index == null ? this.activeKey : (e.index + 1).toString()
          } else {
            console.error(e)
          }
        })
    },

    /** 整理成formData */
    classifyIntoFormData(allValues) {
      let orderMain = Object.assign(this.model, allValues.formValue)
      return {
        ...orderMain, // 展开
        salOdrdList: allValues.tablesValue[0].values
      }
    },

    /** 发起新增或修改的请求 */
    requestAddOrEdit(formData) {
      let obj
      if (!this.model.id) {
        obj = addOrderForm(formData)
      } else {
        obj = editOrderForm(formData)
      }
      this.confirmLoading = true
      obj
        .then(res => {
          if (res.success) {
            this.$message.success(res.message)
            this.$emit('ok')
            this.close()
          } else {
            this.$message.warning(res.message)
          }
        })
        .finally(() => {
          this.confirmLoading = false
        })
    },

    handleSubmit() {
      this.validateFields()
    },
    handleCancel() {
      this.close()
    },
    /**订单信息自动匹配 */
    informationOnChange(e) {
      if (e.type == 'input') {
        let i = smart(e.target.value)
        const province = i.province ? i.province : ''
        const city = i.city ? i.city : ''
        const region = i.address ? i.address : ''
        this.model.province = province
        this.model.city = region
        this.model.region = city
        this.model.address = province + ' ' + city + ' ' + region
      }
    },
    // 点击市弹出AddressModal
    handleChangeForCity() {
      this.$refs.addressModal.visible = true
      this.$refs.addressModal.loadData()
    },
    // 获取子模块地址，赋值给市、地区、省/市区省
    getAddress(data) {
      const province = data.FProvince ? data.FProvince : ''
      const region = data.FRegion ? data.FRegion : ''
      const city = data.FCity ? data.FCity : ''
      this.model.province = province
      this.model.city = city
      this.model.region = region
      this.model.address = province + ' ' + region + ' ' + city
    },
    handleValueChange(event) {
      const { type, row, column, value, target } = event
      if (column.key === 'fproductdesc') {
        getPriceByPriceIdAndOrderDate({priceId: row.fpriceid, 
          orderDate: moment(this.model.orderDate).format("YYYY-MM-DD")}).then(res => {
          if (res.success) {
            const _price = res.result;
            target.setValues([
              {
                rowKey: row.id,
                values: {
                  price: _price ? _price : '', 
                }
              }
            ])
          }
        })
        
      } else if (column.key === 'len' || column.key === 'width' || column.key === 'quality' || column.key === 'price') {
        const _area = ((row.len * row.width) / 10000) * row.quality
        const _unitPriceVal = ((row.len * row.width) / 10000) * row.price
        const _total = ((row.len * row.width) / 10000) * row.quality * row.price
        target.setValues([
          {
            rowKey: row.id,
            values: {
              area: _area ? _area.toFixed(2) : '',
              unitPrice: _unitPriceVal ? _unitPriceVal.toFixed(2) : '',
              total: _total ? _total.toFixed(2) : ''
            }
          }
        ])
      }
    },
    getOrderNumber() {
      const _time = new Date()
      const _year = _time.getFullYear()
      const _month = _time.getMonth() + 1
      const formatYear = _year.toString().substr(2, _year.length)
      const formatMonth = _month < 9 ? '0' + _month : _month
      let number = 'CD' + formatYear + formatMonth + _time.getDate() + _time.getHours()
      this.model.orderNumber = number
    },
    onOrderDateChange: function (value, dateString) {
      this.getAllTable().then(editableTables => {
        editableTables[0].getAll().then((res) => {
          const result = res.values;
          for (let i = 0; i < result.length; i++) {
            getPriceByPriceIdAndOrderDate({priceId: result[i].fpriceid, 
              orderDate: dateString}).then(res => {
              if (res.success) {
                const _price = res.result;
                const _area = ((result[i].len * result[i].width) / 10000) * result[i].quality
                const _unitPriceVal = ((result[i].len * result[i].width) / 10000) * _price
                const _total = ((result[i].len * result[i].width) / 10000) * result[i].quality * _price
                editableTables[0].setValues([
                  {
                    rowKey: result[i].id,
                    values: {
                      price: _price ? _price : '',
                      area: _area ? _area.toFixed(2) : '',
                      unitPrice: _unitPriceVal ? _unitPriceVal.toFixed(2) : '',
                      total: _total ? _total.toFixed(2) : ''
                    }
                  }
                ])
              }
            })
          }
        })
      });
    }
  }
}
</script>

<style scoped>
.upload-list-inline >>> .ant-upload-list-item {
  float: left;
  width: 200px;
  margin-right: 8px;
}
.ant-row {
  margin-bottom: 0px;
}
</style>
