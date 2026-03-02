<script setup lang="ts">
import { onMounted, ref, computed } from 'vue';
import { RouterLink } from 'vue-router';
import { useToolsStore } from '@/store/modules/tools';

const props = defineProps({
  title: {
    type: String,
    default: '推荐工具'
  },
  cateId: {
    type: Number,
    default: 0
  },
  currentUrl: {
    type: String,
    default: ''
  }
});

const toolsStore = useToolsStore();
const recommendedTools = ref([]);

// 随机打乱数组
const shuffleArray = (array: any[]) => {
  const shuffled = [...array];
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
};

// 计算推荐工具
const getRecommendedTools = () => {
  if (!props.cateId) return [];
  
  // 获取当前分类下的所有工具
  let allTools = [];
  toolsStore.cates.forEach(cate => {
    if (cate.id === props.cateId) {
      allTools = cate.list;
    }
  });
  
  // 过滤掉当前工具
  const otherTools = allTools.filter(tool => {
    const toolUrl = tool.url.replace(/\/$/, '');
    const currentUrl = props.currentUrl.replace(/\/$/, '');
    return toolUrl !== currentUrl;
  });
  
  // 随机打乱并取前4个
  const shuffled = shuffleArray(otherTools);
  return shuffled.slice(0, 4);
};

onMounted(() => {
  // 加载工具分类
  toolsStore.getToolCate().then(() => {
    recommendedTools.value = getRecommendedTools();
  });
});
</script>

<template>
  <!-- 推荐工具 -->
  <div class="mt-3 rounded-2xl bg-white p-4">
    <el-divider content-position="left">{{ props.title }}</el-divider>
    <div class="m-4">
      <div v-if="recommendedTools.length > 0" class="grid grid-cols-2 md:grid-cols-4 gap-4">
        <router-link 
          v-for="tool in recommendedTools" 
          :key="tool.url" 
          :to="tool.url"
          class="flex flex-col items-center p-3 border rounded-lg hover:shadow-md transition-shadow"
        >
          <el-image :src="tool.logo" class="w-12 h-12 mb-2 rounded-full"></el-image>
          <div class="text-center">
            <div class="font-medium text-sm line-clamp-1">{{ tool.title }}</div>
          </div>
        </router-link>
      </div>
      <div v-else class="text-center text-gray-500 py-4">
        暂无推荐工具
      </div>
    </div> 
  </div>
</template>

<style scoped>

</style>