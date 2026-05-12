<script setup>
import { ref, computed } from 'vue'
import { ElMessageBox } from 'element-plus'

const props = defineProps({
  items: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['send-exchange'])

const selectedCategory = ref('all')
const showExchangeModal = ref(false)
const currentItem = ref(null)
const exchangeMessage = ref('')

const categories = ['all', '数码', '图书', '生活用品']

const filteredItems = computed(() => {
  if (selectedCategory.value === 'all') {
    return props.items
  }
  return props.items.filter(item => item.category === selectedCategory.value)
})

const openExchangeModal = (item) => {
  currentItem.value = item
  exchangeMessage.value = ''
  showExchangeModal.value = true
}

const submitExchangeRequest = () => {
  if (!exchangeMessage.value.trim()) {
    return
  }
  emit('send-exchange', {
    itemId: currentItem.value.id,
    itemName: currentItem.value.name,
    message: exchangeMessage.value.trim()
  })
  showExchangeModal.value = false
  exchangeMessage.value = ''
}
</script>

<template>
  <div class="item-list-container">
    <div class="filter-bar">
      <span class="filter-label">分类筛选：</span>
      <el-radio-group v-model="selectedCategory">
        <el-radio-button value="all">全部</el-radio-button>
        <el-radio-button value="数码">数码</el-radio-button>
        <el-radio-button value="图书">图书</el-radio-button>
        <el-radio-button value="生活用品">生活用品</el-radio-button>
      </el-radio-group>
    </div>

    <div class="items-grid">
      <el-card 
        v-for="item in filteredItems" 
        :key="item.id"
        class="item-card"
        shadow="hover"
      >
        <div class="item-image">
          <img :src="item.image" :alt="item.name" />
          <el-tag 
            class="type-tag" 
            :type="item.type === 'sale' ? 'success' : 'warning'"
            effect="dark"
          >
            {{ item.type === 'sale' ? '直接出售' : '以物易物' }}
          </el-tag>
        </div>
        <div class="item-content">
          <h3 class="item-title">{{ item.name }}</h3>
          <p class="item-desc">{{ item.description }}</p>
          <div class="item-meta">
            <el-tag size="small" type="info">{{ item.condition }}</el-tag>
            <span v-if="item.type === 'sale'" class="price">¥{{ item.price }}</span>
          </div>
          <div class="item-owner">
            <img :src="item.owner.avatar" class="owner-avatar" />
            <span class="owner-name">{{ item.owner.nickname }}</span>
          </div>
          <el-button 
            type="primary" 
            class="want-btn"
            @click="openExchangeModal(item)"
          >
            {{ item.type === 'sale' ? '我想要购买' : '发起交换' }}
          </el-button>
        </div>
      </el-card>
    </div>

    <el-dialog 
      v-model="showExchangeModal" 
      title="发起交换请求"
      width="500px"
    >
      <div v-if="currentItem" class="exchange-modal-content">
        <div class="item-preview">
          <img :src="currentItem.image" class="preview-img" />
          <div class="preview-info">
            <h4>{{ currentItem.name }}</h4>
            <p class="preview-desc">{{ currentItem.description }}</p>
          </div>
        </div>
        <el-form label-position="top">
          <el-form-item label="你的留言：">
            <el-input
              v-model="exchangeMessage"
              type="textarea"
              :rows="4"
              :placeholder="currentItem.type === 'sale' ? '说点什么，比如什么时候方便交易...' : '介绍下你想用来交换的物品吧~'"
            />
          </el-form-item>
        </el-form>
      </div>
      <template #footer>
        <el-button @click="showExchangeModal = false">取消</el-button>
        <el-button type="primary" @click="submitExchangeRequest">发送请求</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<style scoped>
.item-list-container {
  width: 100%;
}

.filter-bar {
  background: white;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 24px;
  display: flex;
  align-items: center;
  gap: 12px;
}

.filter-label {
  font-weight: 500;
  color: #303133;
}

.items-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;
}

.item-card {
  border-radius: 12px;
  overflow: hidden;
  transition: transform 0.3s;
}

.item-card:hover {
  transform: translateY(-4px);
}

.item-image {
  width: 100%;
  height: 200px;
  position: relative;
  overflow: hidden;
}

.item-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.type-tag {
  position: absolute;
  top: 12px;
  left: 12px;
}

.item-content {
  padding: 16px;
}

.item-title {
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 8px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.item-desc {
  font-size: 14px;
  color: #606266;
  margin-bottom: 12px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.item-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.price {
  font-size: 18px;
  font-weight: bold;
  color: #f56c6c;
}

.item-owner {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
}

.owner-avatar {
  width: 28px;
  height: 28px;
  border-radius: 50%;
}

.owner-name {
  font-size: 13px;
  color: #909399;
}

.want-btn {
  width: 100%;
}

.exchange-modal-content {
  padding: 10px 0;
}

.item-preview {
  display: flex;
  gap: 16px;
  padding-bottom: 16px;
  border-bottom: 1px solid #ebeef5;
  margin-bottom: 16px;
}

.preview-img {
  width: 100px;
  height: 100px;
  object-fit: cover;
  border-radius: 8px;
}

.preview-info h4 {
  font-size: 16px;
  color: #303133;
  margin-bottom: 6px;
}

.preview-desc {
  font-size: 13px;
  color: #909399;
}
</style>
