<script setup lang="ts">
import { ref } from 'vue'
import DetailHeader from '@/components/Layout/DetailHeader/DetailHeader.vue'
import ToolDetail from '@/components/Layout/ToolDetail/ToolDetail.vue'
import { ElMessage } from 'element-plus'
import { copy } from '@/utils/string'

const title = "AES加解密"

// 状态管理
const activeTab = ref('encrypt')
const inputText = ref('')
const outputText = ref('')
const key = ref('')
const iv = ref('')
const mode = ref('CBC')
const keySize = ref(128)

// 加密模式选项
const modeOptions = [
  { label: 'CBC', value: 'CBC' },
  { label: 'ECB', value: 'ECB' }
]

// 密钥长度选项
const keySizeOptions = [
  { label: '128位', value: 128 },
  { label: '192位', value: 192 },
  { label: '256位', value: 256 }
]

// 加密处理
const encrypt = async () => {
  if (!inputText.value) {
    ElMessage.warning('请输入要加密的文本')
    return
  }
  
  if (!key.value) {
    ElMessage.warning('请输入密钥')
    return
  }
  
  if (mode.value === 'CBC' && !iv.value) {
    ElMessage.warning('CBC模式需要输入初始化向量(IV)')
    return
  }
  
  try {
    // 生成密钥
    const cryptoKey = await generateKey(key.value, keySize.value)
    
    // 加密
    const encrypted = await aesEncrypt(inputText.value, cryptoKey, iv.value, mode.value)
    outputText.value = btoa(encrypted)
    
    ElMessage.success('加密成功')
  } catch (error) {
    ElMessage.error('加密失败：' + error)
  }
}

// 解密处理
const decrypt = async () => {
  if (!inputText.value) {
    ElMessage.warning('请输入要解密的文本')
    return
  }
  
  if (!key.value) {
    ElMessage.warning('请输入密钥')
    return
  }
  
  if (mode.value === 'CBC' && !iv.value) {
    ElMessage.warning('CBC模式需要输入初始化向量(IV)')
    return
  }
  
  try {
    // 生成密钥
    const cryptoKey = await generateKey(key.value, keySize.value)
    
    // 解密
    const decrypted = await aesDecrypt(atob(inputText.value), cryptoKey, iv.value, mode.value)
    outputText.value = decrypted
    
    ElMessage.success('解密成功')
  } catch (error) {
    ElMessage.error('解密失败：' + error)
  }
}

// 生成密钥
const generateKey = async (keyStr: string, size: number) => {
  // 使用SHA-256哈希密钥，确保长度正确
  const encoder = new TextEncoder()
  const keyData = encoder.encode(keyStr)
  const hashBuffer = await crypto.subtle.digest('SHA-256', keyData)
  const hashArray = Array.from(new Uint8Array(hashBuffer))
  
  // 根据密钥长度截取
  const keyBytes = size / 8
  const keyBuffer = new Uint8Array(hashArray.slice(0, keyBytes))
  
  return await crypto.subtle.importKey(
    'raw',
    keyBuffer,
    { name: 'AES-' + mode.value },
    false,
    ['encrypt', 'decrypt']
  )
}

// AES加密
const aesEncrypt = async (text: string, key: CryptoKey, ivStr: string, mode: string) => {
  const encoder = new TextEncoder()
  const data = encoder.encode(text)
  
  let ivBuffer: Uint8Array
  if (mode === 'CBC') {
    // 使用SHA-256哈希IV，确保长度为16字节
    const ivData = encoder.encode(ivStr)
    const ivHashBuffer = await crypto.subtle.digest('SHA-256', ivData)
    ivBuffer = new Uint8Array(ivHashBuffer.slice(0, 16))
  }
  
  const algorithm: AlgorithmIdentifier = mode === 'CBC' 
    ? { name: 'AES-CBC', iv: ivBuffer! } as AesCbcParams
    : { name: 'AES-ECB' }
  
  const encrypted = await crypto.subtle.encrypt(algorithm, key, data)
  return String.fromCharCode(...new Uint8Array(encrypted))
}

// AES解密
const aesDecrypt = async (text: string, key: CryptoKey, ivStr: string, mode: string) => {
  const data = new Uint8Array(text.split('').map(char => char.charCodeAt(0)))
  
  let ivBuffer: Uint8Array
  if (mode === 'CBC') {
    // 使用SHA-256哈希IV，确保长度为16字节
    const encoder = new TextEncoder()
    const ivData = encoder.encode(ivStr)
    const ivHashBuffer = await crypto.subtle.digest('SHA-256', ivData)
    ivBuffer = new Uint8Array(ivHashBuffer.slice(0, 16))
  }
  
  const algorithm: AlgorithmIdentifier = mode === 'CBC' 
    ? { name: 'AES-CBC', iv: ivBuffer! } as AesCbcParams
    : { name: 'AES-ECB' }
  
  const decrypted = await crypto.subtle.decrypt(algorithm, key, data)
  const decoder = new TextDecoder()
  return decoder.decode(decrypted)
}

// 复制结果
const copyResult = () => {
  if (!outputText.value) {
    ElMessage.warning('没有可复制的内容')
    return
  }
  
  copy(outputText.value)
}

// 清空输入
const clearInput = () => {
  inputText.value = ''
  outputText.value = ''
}
</script>

<template>
  <div class="flex flex-col mt-3 flex-1">
    <DetailHeader :title="title"></DetailHeader>

    <div class="p-4 rounded-2xl bg-white">
      <el-tabs v-model="activeTab">
        <el-tab-pane label="加密" name="encrypt">
          <!-- 输入部分 -->
          <div class="mb-4">
            <el-input
              v-model="inputText"
              type="textarea"
              :rows="4"
              placeholder="请输入要加密的文本"
              class="mb-2"
            />
            
            <el-input
              v-model="key"
              placeholder="请输入密钥"
              class="mb-2"
            />
            
            <el-input
              v-model="iv"
              placeholder="请输入初始化向量(IV) (CBC模式必填)"
              class="mb-2"
            />
            
            <div class="flex gap-4 items-center mb-2">
              <div class="flex items-center gap-2">
                <el-text>加密模式：</el-text>
                <el-select v-model="mode" style="width: 120px;">
                  <el-option
                    v-for="option in modeOptions"
                    :key="option.value"
                    :label="option.label"
                    :value="option.value"
                  />
                </el-select>
              </div>
              
              <div class="flex items-center gap-2">
                <el-text>密钥长度：</el-text>
                <el-select v-model="keySize" style="width: 120px;">
                  <el-option
                    v-for="option in keySizeOptions"
                    :key="option.value"
                    :label="option.label"
                    :value="option.value"
                  />
                </el-select>
              </div>
            </div>
          </div>
          
          <!-- 操作按钮 -->
          <div class="mb-4">
            <el-button type="primary" @click="encrypt">加密</el-button>
            <el-button @click="clearInput">清空</el-button>
          </div>
          
          <!-- 结果部分 -->
          <div>
            <el-input
              v-model="outputText"
              type="textarea"
              :rows="4"
              placeholder="加密结果"
              readonly
            >
              <template #append>
                <el-button @click="copyResult">复制</el-button>
              </template>
            </el-input>
          </div>
        </el-tab-pane>
        
        <el-tab-pane label="解密" name="decrypt">
          <!-- 输入部分 -->
          <div class="mb-4">
            <el-input
              v-model="inputText"
              type="textarea"
              :rows="4"
              placeholder="请输入要解密的文本"
              class="mb-2"
            />
            
            <el-input
              v-model="key"
              placeholder="请输入密钥"
              class="mb-2"
            />
            
            <el-input
              v-model="iv"
              placeholder="请输入初始化向量(IV) (CBC模式必填)"
              class="mb-2"
            />
            
            <div class="flex gap-4 items-center mb-2">
              <div class="flex items-center gap-2">
                <el-text>加密模式：</el-text>
                <el-select v-model="mode" class="w-32">
                  <el-option
                    v-for="option in modeOptions"
                    :key="option.value"
                    :label="option.label"
                    :value="option.value"
                  />
                </el-select>
              </div>
              
              <div class="flex items-center gap-2">
                <el-text>密钥长度：</el-text>
                <el-select v-model="keySize" class="w-32">
                  <el-option
                    v-for="option in keySizeOptions"
                    :key="option.value"
                    :label="option.label"
                    :value="option.value"
                  />
                </el-select>
              </div>
            </div>
          </div>
          
          <!-- 操作按钮 -->
          <div class="mb-4">
            <el-button type="primary" @click="decrypt">解密</el-button>
            <el-button @click="clearInput">清空</el-button>
          </div>
          
          <!-- 结果部分 -->
          <div>
            <el-input
              v-model="outputText"
              type="textarea"
              :rows="4"
              placeholder="解密结果"
              readonly
            >
              <template #append>
                <el-button @click="copyResult">复制</el-button>
              </template>
            </el-input>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>

    <!-- desc -->
    <ToolDetail title="描述">
      <el-text>
        在线AES加解密工具，支持CBC和ECB模式，支持128位、192位、256位密钥长度，可用于数据加密和解密。
      </el-text>
    </ToolDetail>

  </div>
</template>

<style scoped>

</style>