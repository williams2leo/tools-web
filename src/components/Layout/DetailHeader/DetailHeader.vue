<script setup lang="ts">
import { Star, StarFilled } from '@element-plus/icons-vue'
import { onMounted, reactive, ref, computed } from 'vue';
import { useRoute } from 'vue-router'
import { useToolsStore } from '@/store/modules/tools'
import { ElMessage } from 'element-plus'
import {rtrim} from '@/utils/string'
const props = defineProps({  title: String,  id: Number})
const route = useRoute()
//查询参数
const searchParam = reactive({
  cateId: 0,
  title: '',
  route: ''
})
//store
const toolsStore = useToolsStore()
const isCollected = ref(false)

//根据路由查询tool id
const getToolInfo = async () => {
  let routeStr = route.path
  searchParam.route = rtrim(routeStr, '/')
  await toolsStore.getToolInfo(searchParam)
  // 检查收藏状态
  isCollected.value = toolsStore.isCollected(searchParam.route)
}

// 收藏/取消收藏
const toggleCollect = () => {
  const toolInfo = toolsStore.currentTool
  if (!toolInfo) {
    ElMessage.warning('获取工具信息失败');
    return;
  }
  
  if (isCollected.value) {
    // 取消收藏
    const success = toolsStore.removeCollect(toolInfo.url)
    if (success) {
      isCollected.value = false
      ElMessage.success('已取消收藏');
    } else {
      ElMessage.error('取消收藏失败');
    }
  } else {
    // 添加收藏
    const success = toolsStore.addCollect(toolInfo)
    if (success) {
      isCollected.value = true
      ElMessage.success('收藏成功');
    } else {
      ElMessage.error('收藏失败，可能已经收藏过了');
    }
  }
}

onMounted(() => {
  toolsStore.loadCollectedTools()
  getToolInfo()
})

</script>

<template>
  <div class="flex justify-between items-center rounded-2xl bg-white p-4 mt-5 mb-5">
    <div class="text-xl">
      {{ props.title }}
    </div>
    <div>
      <el-button 
        :icon="isCollected ? StarFilled : Star" 
        :type="isCollected ? 'warning' : 'default'"
        @click="toggleCollect"
      >
        {{ isCollected ? '已收藏' : '收藏' }}
      </el-button>
    </div>
  </div>
</template>

<style scoped>

</style>