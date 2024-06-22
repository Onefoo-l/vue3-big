<script setup>
import { artGetChannelsService, artDelChannelService } from '@/api/article'
import { Delete, Edit } from '@element-plus/icons-vue'
import { ref } from 'vue'
import channelEdit from '@/views/article/components/ChannelEdit.vue'
const channelList = ref([])
// 控制等待效果
const loading = ref(false)
// 显示弹层
// const dialogVisible = ref(false)
// 获取子组件里面的弹层
const dialog = ref()

// 获取文章分类数据
const getChannelList = async () => {
  loading.value = true
  const res = await artGetChannelsService()
  console.log(res)
  channelList.value = res.data.data
  loading.value = false
}

const onDelChannel = async (row) => {
  await ElMessageBox.confirm('你确认删除该分类信息吗？', '温馨提示', {
    type: 'warning',
    confirmButtonText: '确认',
    cancelButtonText: '取消'
  })
  await artDelChannelService(row.id)
  ElMessage({ type: 'success', message: '删除成功' })
  getChannelList()
}
// 点击显示弹层
const onAddChannel = () => {
  // dialogVisible.value = true
  dialog.value.open({})
}
// 点击显示编辑
const onEditChannel = (row) => {
  dialog.value.open(row)
}
const onSuccess = () => {
  getChannelList()
}
getChannelList()
</script>

<template>
  <page-container title="文章分类">
    <template #extra>
      <el-button type="primary" @click="onAddChannel"> 添加分类 </el-button>
    </template>
    <!-- 表格部分 -->
    <el-table :data="channelList" v-loading="loading" style="width: 100%">
      <el-table-column label="序号" width="100" type="index"> </el-table-column>
      <el-table-column label="分类名称" prop="cate_name"></el-table-column>
      <el-table-column label="分类别名" prop="cate_alias"></el-table-column>
      <el-table-column label="操作" width="100">
        <template #default="{ row }">
          <el-button
            :icon="Edit"
            circle
            plain
            type="primary"
            @click="onEditChannel(row)"
          ></el-button>
          <el-button
            :icon="Delete"
            circle
            plain
            type="danger"
            @click="onDelChannel(row)"
          ></el-button>
        </template>
      </el-table-column>
      <template #empty>
        <el-empty description="没有数据" />
      </template>
    </el-table>
  </page-container>
  <!--  弹层部分 -->
  <!-- <el-dialog v-model="dialogVisible" title="添加弹层" width="30%">
    <div>我是内容部分</div>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary"> 确认 </el-button>
      </span>
    </template>
  </el-dialog> -->
  <channel-edit ref="dialog" @success="onSuccess"></channel-edit>
</template>

<style lang="scss" scoped></style>
