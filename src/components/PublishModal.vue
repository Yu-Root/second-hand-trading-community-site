<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['update:visible', 'submit'])

const formData = ref({
  name: '',
  description: '',
  category: '数码',
  condition: '9成新',
  type: 'sale',
  price: '',
  swapDesc: '',
  image: ''
})

const imageOptions = [
  'https://images.unsplash.com/photo-1611532736597-de2d4265fba3?w=400&h=300&fit=crop',
  'https://images.unsplash.com/photo-1544947950-fa07a98d237f?w=400&h=300&fit=crop',
  'https://images.unsplash.com/photo-1507473885765-e6ed057f782c?w=400&h=300&fit=crop',
  'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400&h=300&fit=crop',
  'https://images.unsplash.com/photo-1544367567-0f2fcb009e0b?w=400&h=300&fit=crop',
  'https://images.unsplash.com/photo-1532012197267-da84d127e765?w=400&h=300&fit=crop'
]

watch(() => props.visible, (val) => {
  if (!val) {
    resetForm()
  }
})

const resetForm = () => {
  formData.value = {
    name: '',
    description: '',
    category: '数码',
    condition: '9成新',
    type: 'sale',
    price: '',
    swapDesc: '',
    image: ''
  }
}

const handleSubmit = () => {
  const data = { ...formData.value }
  if (!data.image) {
    data.image = imageOptions[Math.floor(Math.random() * imageOptions.length)]
  }
  emit('submit', data)
  emit('update:visible', false)
}
</script>

<template>
  <el-dialog 
    :model-value="visible" 
    @update:model-value="emit('update:visible', $event)"
    title="✨ 发布闲置物品"
    width="600px"
    :close-on-click-modal="false"
  >
    <el-form :model="formData" label-width="100px">
      <el-form-item label="物品名称" required>
        <el-input v-model="formData.name" placeholder="请输入物品名称" />
      </el-form-item>
      <el-form-item label="物品描述" required>
        <el-input 
          v-model="formData.description" 
          type="textarea" 
          :rows="3"
          placeholder="详细描述一下你的物品吧~" 
        />
      </el-form-item>
      <el-form-item label="物品分类" required>
        <el-select v-model="formData.category" style="width: 100%">
          <el-option label="数码" value="数码" />
          <el-option label="图书" value="图书" />
          <el-option label="生活用品" value="生活用品" />
        </el-select>
      </el-form-item>
      <el-form-item label="新旧程度" required>
        <el-select v-model="formData.condition" style="width: 100%">
          <el-option label="全新" value="全新" />
          <el-option label="99新" value="99新" />
          <el-option label="95新" value="95新" />
          <el-option label="9成新" value="9成新" />
          <el-option label="8成新" value="8成新" />
        </el-select>
      </el-form-item>
      <el-form-item label="交易类型" required>
        <el-radio-group v-model="formData.type">
          <el-radio value="sale">直接出售</el-radio>
          <el-radio value="swap">以物易物</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item v-if="formData.type === 'sale'" label="出售价格">
        <el-input v-model="formData.price" placeholder="例如：50元" />
      </el-form-item>
      <el-form-item v-if="formData.type === 'swap'" label="交换说明">
        <el-input v-model="formData.swapDesc" placeholder="说明你希望交换什么物品" />
      </el-form-item>
      <el-form-item label="物品图片">
        <div class="image-selector">
          <div 
            v-for="(img, idx) in imageOptions" 
            :key="idx"
            class="image-option"
            :class="{ active: formData.image === img }"
            @click="formData.image = img"
          >
            <img :src="img" />
          </div>
        </div>
        <el-input 
          v-model="formData.image" 
          placeholder="或直接输入图片链接"
          style="margin-top: 10px"
        />
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="emit('update:visible', false)">取消</el-button>
      <el-button type="primary" @click="handleSubmit">发布物品</el-button>
    </template>
  </el-dialog>
</template>

<style scoped>
.image-selector {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.image-option {
  width: 80px;
  height: 60px;
  border-radius: 6px;
  overflow: hidden;
  border: 2px solid transparent;
  cursor: pointer;
  transition: all 0.3s;
}

.image-option.active {
  border-color: #409eff;
}

.image-option img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>
