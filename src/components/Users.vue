<template>
    <div>
        <!-- 导航 -->
        <el-breadcrumb separator="/">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item><a href="/">用户管理</a></el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item>
        </el-breadcrumb>

        <!-- 卡片视图 -->
        <el-card>
            <!-- 搜索与添加 -->
            <el-row :gutter="20">
                <el-col :span="8">
                    <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUserList">
                        <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="4">
                    <el-button type="primary">添加用户</el-button>
                </el-col>
            </el-row>

            <!-- 用户列表区域 -->
            <el-table :data="userlist" border stripe>
                <el-table-column label="#" type="index"></el-table-column>
                <el-table-column label="姓名"  prop="username"></el-table-column>
                <el-table-column label="邮箱"  prop="email"></el-table-column>
                <el-table-column label="电话"  prop="mobile"></el-table-column>
                <el-table-column label="角色"  prop="role_name"></el-table-column>
                <el-table-column label="状态"  prop="mg_state">
                  <template v-slot="slot">
                    <el-switch v-model="slot.row.mg_state" @change="userStateChanged(slot.row)"></el-switch>
                  </template>
                </el-table-column>
                <el-table-column label="操作" width="175px">
                  <template>
                    <!-- 修改按钮 -->
                    <el-tooltip effect="dark" content="修改信息" placement="top" :enterable="false">
                      <el-button type="primary" icon="el-icon-edit" size="mini"></el-button>
                    </el-tooltip>
                    <!-- 删除按钮 -->
                    <el-tooltip effect="dark" content="删除用户" placement="top" :enterable="false">
                      <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
                    </el-tooltip>
                    <!-- 分配按钮 -->
                    <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
                      <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
                    </el-tooltip>
                  </template>
                </el-table-column>
            </el-table>
            <!-- 分页区域 -->
            <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
            :current-page="queryInfo.pagenum" :page-sizes="[2, 5, 10, 20]" :page-size="queryInfo.pagesize"
            layout="total, sizes, prev, pager, next, jumper" :total="total">
            </el-pagination>
        </el-card>
    </div>
</template>

<script>
export default {
  data () {
    return {
      // 获取用户列表的参数对象
      queryInfo: {
        query: '',
        // 当前的页数
        pagenum: 1,
        // 当前每页显示多少条数据
        pagesize: 2
      },
      userlist: [],
      total: 0
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList () {
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取用户列表失败！')
      }
      this.userlist = res.data.users
      this.total = res.data.total
    },
    // 监听pagesize改变的事件
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getUserList()
    },
    // 监听页码改变的事件
    handleCurrentChange (newPage) {
      this.queryInfo.pagenum = newPage
      this.getUserList()
    },
    // 监听switch开关状态改变
    async userStateChanged (userinfo) {
      console.log(userinfo)
      const { data: res } = await this.$http.put(`users/${userinfo.id}/state/${userinfo.mg_state}`)
      if (res.meta.status !== 200) {
        userinfo.mg_state = !userinfo.mg_state
        return this.$message.error('更新用户状态失败！')
      }
      this.$message.success('更新用户状成功！')
    }
  }
}
</script>

<style lang="less" scoped>
</style>
