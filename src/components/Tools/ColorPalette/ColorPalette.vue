<template>
  <div class="flex flex-col mt-3 flex-1">
    <DetailHeader :title="title"></DetailHeader>

    <div class="p-4 rounded-2xl bg-white">
      <div class="tool-content">
        <div class="input-section">
          <el-tabs v-model="activeCategory">
            <el-tab-pane label="基础颜色" name="basic">
              <div class="color-grid">
                <div 
                  v-for="color in basicColors" 
                  :key="color.hex"
                  class="color-item"
                  :style="{ backgroundColor: color.hex }"
                  @click="copyColor(color)"
                >
                  <div class="color-info">
                    <span class="color-name">{{ color.name }}</span>
                    <span class="color-hex">{{ color.hex }}</span>
                  </div>
                </div>
              </div>
            </el-tab-pane>
            
            <el-tab-pane label="渐变色" name="gradient">
              <div class="gradient-grid">
                <div 
                  v-for="(gradient, index) in gradients" 
                  :key="index"
                  class="gradient-item"
                  :style="{ background: gradient.value }"
                  @click="copyGradient(gradient)"
                >
                  <div class="gradient-info">
                    <span class="gradient-name">{{ gradient.name }}</span>
                    <span class="gradient-value">{{ gradient.value }}</span>
                  </div>
                </div>
              </div>
            </el-tab-pane>
            
            <el-tab-pane label="Material Design" name="material">
              <div class="color-grid">
                <div 
                  v-for="color in materialColors" 
                  :key="color.hex"
                  class="color-item"
                  :style="{ backgroundColor: color.hex }"
                  @click="copyColor(color)"
                >
                  <div class="color-info">
                    <span class="color-name">{{ color.name }}</span>
                    <span class="color-hex">{{ color.hex }}</span>
                  </div>
                </div>
              </div>
            </el-tab-pane>
            
            <el-tab-pane label="扁平化" name="flat">
              <div class="color-grid">
                <div 
                  v-for="color in flatColors" 
                  :key="color.hex"
                  class="color-item"
                  :style="{ backgroundColor: color.hex }"
                  @click="copyColor(color)"
                >
                  <div class="color-info">
                    <span class="color-name">{{ color.name }}</span>
                    <span class="color-hex">{{ color.hex }}</span>
                  </div>
                </div>
              </div>
            </el-tab-pane>
          </el-tabs>
        </div>
      </div>
    </div>

    <!-- desc -->
    <ToolDetail title="描述">
      <el-text>
        在线色板工具，提供基础颜色、渐变色、Material Design 颜色和扁平化颜色，点击颜色可复制颜色值，方便设计和开发使用。
      </el-text>
    </ToolDetail>

  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { ElMessage } from 'element-plus';
import DetailHeader from '@/components/Layout/DetailHeader/DetailHeader.vue'
import ToolDetail from '@/components/Layout/ToolDetail/ToolDetail.vue'

const title = "色板工具"

const activeCategory = ref('basic');

// 基础颜色
const basicColors = [
  { name: '红色', hex: '#FF0000' },
  { name: '绿色', hex: '#00FF00' },
  { name: '蓝色', hex: '#0000FF' },
  { name: '黄色', hex: '#FFFF00' },
  { name: '紫色', hex: '#800080' },
  { name: '橙色', hex: '#FFA500' },
  { name: '黑色', hex: '#000000' },
  { name: '白色', hex: '#FFFFFF' },
  { name: '灰色', hex: '#808080' },
  { name: '棕色', hex: '#A52A2A' },
  { name: '粉色', hex: '#FFC0CB' },
  { name: '青色', hex: '#00FFFF' },
  { name: '品红', hex: '#FF00FF' },
  { name: '橄榄绿', hex: '#808000' },
  { name: '海军蓝', hex: '#000080' },
  { name: '银色', hex: '#C0C0C0' },
];

// 渐变色
const gradients = [
  { name: '蓝到紫', value: 'linear-gradient(90deg, #3B82F6, #8B5CF6)' },
  { name: '红到黄', value: 'linear-gradient(90deg, #EF4444, #F59E0B)' },
  { name: '绿到蓝', value: 'linear-gradient(90deg, #10B981, #3B82F6)' },
  { name: '紫到粉', value: 'linear-gradient(90deg, #8B5CF6, #EC4899)' },
  { name: '黄到绿', value: 'linear-gradient(90deg, #F59E0B, #10B981)' },
  { name: '红到橙', value: 'linear-gradient(90deg, #EF4444, #F97316)' },
  { name: '蓝到青', value: 'linear-gradient(90deg, #3B82F6, #06B6D4)' },
  { name: '紫到红', value: 'linear-gradient(90deg, #8B5CF6, #EF4444)' },
];

// Material Design颜色
const materialColors = [
  { name: 'Material Red', hex: '#F44336' },
  { name: 'Material Pink', hex: '#E91E63' },
  { name: 'Material Purple', hex: '#9C27B0' },
  { name: 'Material Deep Purple', hex: '#673AB7' },
  { name: 'Material Indigo', hex: '#3F51B5' },
  { name: 'Material Blue', hex: '#2196F3' },
  { name: 'Material Light Blue', hex: '#03A9F4' },
  { name: 'Material Cyan', hex: '#00BCD4' },
  { name: 'Material Teal', hex: '#009688' },
  { name: 'Material Green', hex: '#4CAF50' },
  { name: 'Material Light Green', hex: '#8BC34A' },
  { name: 'Material Lime', hex: '#CDDC39' },
  { name: 'Material Yellow', hex: '#FFEB3B' },
  { name: 'Material Amber', hex: '#FFC107' },
  { name: 'Material Orange', hex: '#FF9800' },
  { name: 'Material Deep Orange', hex: '#FF5722' },
];

// 扁平化颜色
const flatColors = [
  { name: 'Flat Turquoise', hex: '#1ABC9C' },
  { name: 'Flat Green Sea', hex: '#16A085' },
  { name: 'Flat Peter River', hex: '#3498DB' },
  { name: 'Flat Belize Hole', hex: '#2980B9' },
  { name: 'Flat Amethyst', hex: '#9B59B6' },
  { name: 'Flat Wisteria', hex: '#8E44AD' },
  { name: 'Flat Wet Asphalt', hex: '#34495E' },
  { name: 'Flat Midnight Blue', hex: '#2C3E50' },
  { name: 'Flat Sun Flower', hex: '#F1C40F' },
  { name: 'Flat Orange', hex: '#F39C12' },
  { name: 'Flat Carrot', hex: '#E67E22' },
  { name: 'Flat Alizarin', hex: '#E74C3C' },
  { name: 'Flat Clouds', hex: '#ECF0F1' },
  { name: 'Flat Silver', hex: '#BDC3C7' },
  { name: 'Flat Concrete', hex: '#95A5A6' },
  { name: 'Flat Asbestos', hex: '#7F8C8D' },
];

// 复制颜色
const copyColor = (color: { name: string; hex: string }) => {
  navigator.clipboard.writeText(color.hex).then(() => {
    ElMessage.success(`已复制 ${color.name} 的颜色值: ${color.hex}`);
  }).catch(() => {
    ElMessage.error('复制失败');
  });
};

// 复制渐变色
const copyGradient = (gradient: { name: string; value: string }) => {
  navigator.clipboard.writeText(gradient.value).then(() => {
    ElMessage.success(`已复制 ${gradient.name} 的渐变值: ${gradient.value}`);
  }).catch(() => {
    ElMessage.error('复制失败');
  });
};
</script>

<style scoped>
.tool-content {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.input-section {
  width: 100%;
}

.color-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 15px;
  margin-top: 20px;
}

.color-item {
  height: 120px;
  border-radius: 12px;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.color-item:hover {
  transform: translateY(-5px);
}

.color-info {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 8px;
  width: 100%;
  text-align: center;
  transition: all 0.3s ease;
}

.color-name {
  display: block;
  font-size: 14px;
  font-weight: bold;
  margin-bottom: 4px;
}

.color-hex {
  display: block;
  font-size: 12px;
  font-family: monospace;
}

.gradient-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 15px;
  margin-top: 20px;
}

.gradient-item {
  height: 120px;
  border-radius: 12px;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.gradient-item:hover {
  transform: translateY(-5px);
}

.gradient-info {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 8px;
  width: 100%;
  text-align: center;
  transition: all 0.3s ease;
}

.gradient-name {
  display: block;
  font-size: 14px;
  font-weight: bold;
  margin-bottom: 4px;
}

.gradient-value {
  display: block;
  font-size: 12px;
  font-family: monospace;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  padding: 0 10px;
}

@media (max-width: 768px) {
  .color-grid {
    grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  }
  
  .gradient-grid {
    grid-template-columns: 1fr;
  }
}
</style>