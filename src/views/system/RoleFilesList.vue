<template>
  <a-row>
    <a-col :span="24">
      <a-card :bordered="false">
        <!-- 查询区域 -->
        <div class="search">
          <a-form @keyup.enter.native="searchQuery">
            <a-row :gutter="24">
              <a-col :md="6">
                <a-form-item label="角色编码" :labelCol="{ span: 5 }" :wrapperCol="{ span: 18, offset: 1 }">
                  <a-input placeholder="" allow-clear v-model="queryParam.roleCode"></a-input>
                </a-form-item>
              </a-col>
              <a-col :md="6">
                <a-form-item label="角色名称" :labelCol="{ span: 5 }" :wrapperCol="{ span: 18, offset: 1 }">
                  <a-input placeholder="" allow-clear v-model="queryParam.roleName"></a-input>
                </a-form-item>
              </a-col>
              <a-col :md="12">
                <a-button type="primary" @click="searchQuery" icon="search" style="margin-left: 21px">查询</a-button>
                <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              </a-col>
            </a-row>
          </a-form>
        </div>
        <!-- 操作按钮区域 -->
        <div class="operator">
          <a-button @click="handleAdd" type="primary" icon="plus">新建角色</a-button>
        </div>
        <!-- table区域 -->
        <div style="margin-top: 15px">
          <a-table
            bordered
            size="middle"
            rowKey="id"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="ipagination"
            :loading="loading"
            @change="handleTableChange"
          >
            <span slot="action" slot-scope="text, record">
              <a @click="handleEdit(record)">编辑</a>
              <a-divider type="vertical" />
              <a-popconfirm title="确定删除吗?" @confirm="() => handleDel(record.id)">
                <a>删除</a>
              </a-popconfirm>
              <a-divider type="vertical" />
              <a @click="handlePerssion(record.id)">授权</a>
            </span>
          </a-table>
        </div>

        <user-role-modal ref="modalUserRole"></user-role-modal>
        <role-modal ref="modalForm" @ok="modalFormOk"></role-modal>
      </a-card>
    </a-col>
  </a-row>
</template>

<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import UserRoleModal from './modules/UserRoleModal' // 授权modal
import RoleModal from './modules/RoleModal' // 新增、编辑modal
import moment from 'moment' // 日期格式化

export default {
  name: 'RoleFilesList',
  mixins: [JeecgListMixin],
  components: {
    UserRoleModal,
    RoleModal,
    moment
  },
  data() {
    return {
      columns: [
        {
          title: '角色编码',
          align: 'center',
          dataIndex: 'roleCode'
        },
        {
          title: '角色名称',
          align: 'center',
          dataIndex: 'roleName'
        },
        {
          title: '角色描述',
          align: 'center',
          dataIndex: 'description'
        },
        {
          title: '创建时间',
          dataIndex: 'createTime',
          align: 'center',
          sorter: true,
          customRender: text => {
            return moment(text).format('YYYY-MM-DD')
          }
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          scopedSlots: { customRender: 'action' }
        }
      ],
      url: {
        list: '/sys/role/list', // 页面初始化查询
        delete: '/sys/role/delete', // 删除
        addUserRole: '/sys/user/addSysUserRole' // 授权：角色权限保存
      }
    }
  },
  methods: {
    handleDel: function(id) {
      this.handleDelete(id)
    },
    handlePerssion(roleId) {
      this.$refs.modalUserRole.show(roleId)
    }
  }
}
</script>
