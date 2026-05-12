<script setup>
import { ref, reactive, computed } from 'vue'
import { ElMessage } from 'element-plus'
import ItemList from './components/ItemList.vue'
import PublishModal from './components/PublishModal.vue'
import UserProfile from './components/UserProfile.vue'

const currentTab = ref('home')
const showPublishModal = ref(false)

const currentUser = reactive({
  id: 1,
  nickname: '小跳蚤',
  avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user1',
  bio: '热爱生活，喜欢交换闲置物品~'
})

const items = ref([
  {
    id: 1,
    name: 'Kindle Paperwhite 电子书阅读器',
    description: '成色很新，使用不到半年，带保护壳和原装充电器',
    category: '数码',
    condition: '95新',
    type: 'sale',
    price: '350元',
    image: 'https://images.unsplash.com/photo-1611532736597-de2d4265fba3?w=400&h=300&fit=crop',
    owner: { id: 2, nickname: '书虫小明', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user2' },
    createdAt: '2024-01-15'
  },
  {
    id: 2,
    name: '《百年孤独》精装版',
    description: '正版书籍，几乎全新，想换一本类似的文学作品',
    category: '图书',
    condition: '全新',
    type: 'swap',
    swapDesc: '希望换一本你闲置的好书',
    image: 'https://images.unsplash.com/photo-1544947950-fa07a98d237f?w=400&h=300&fit=crop',
    owner: { id: 3, nickname: '文艺青年', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user3' },
    createdAt: '2024-01-14'
  },
  {
    id: 3,
    name: '宜家简约风台灯',
    description: 'LED护眼台灯，亮度可调，适合学习工作',
    category: '生活用品',
    condition: '9成新',
    type: 'sale',
    price: '45元',
    image: 'https://images.unsplash.com/photo-1507473885765-e6ed057f782c?w=400&h=300&fit=crop',
    owner: { id: 2, nickname: '书虫小明', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user2' },
    createdAt: '2024-01-13'
  },
  {
    id: 4,
    name: 'Sony 无线耳机 WH-1000XM4',
    description: '降噪效果一流，配件齐全，有购买凭证',
    category: '数码',
    condition: '9成新',
    type: 'swap',
    swapDesc: '想换一台成色好的便携投影仪',
    image: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400&h=300&fit=crop',
    owner: { id: 4, nickname: '音乐发烧友', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user4' },
    createdAt: '2024-01-12'
  },
  {
    id: 5,
    name: '运动瑜伽垫套装',
    description: '瑜伽垫+拉力带，几乎没怎么用过',
    category: '生活用品',
    condition: '99新',
    type: 'sale',
    price: '30元',
    image: 'https://images.unsplash.com/photo-1544367567-0f2fcb009e0b?w=400&h=300&fit=crop',
    owner: { id: 5, nickname: '健身达人', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user5' },
    createdAt: '2024-01-11'
  },
  {
    id: 6,
    name: '三体全集 刘慈欣',
    description: '珍藏版套装，三本齐全，想交换科幻类书籍',
    category: '图书',
    condition: '8成新',
    type: 'swap',
    swapDesc: '期待你的科幻小说！',
    image: 'https://images.unsplash.com/photo-1532012197267-da84d127e765?w=400&h=300&fit=crop',
    owner: { id: 3, nickname: '文艺青年', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user3' },
    createdAt: '2024-01-10'
  }
])

const exchangeRequests = ref([
  {
    id: 1,
    itemId: 1,
    itemName: 'Kindle Paperwhite 电子书阅读器',
    requester: { id: 1, nickname: '小跳蚤', avatar: 'https://api.dicebear.com/7.x/avataaars/svg?seed=user1' },
    message: '我可以用我闲置的机械键盘和你交换吗？成色很棒的！',
    createdAt: '2024-01-16',
    status: 'pending'
  }
])

const addItem = (newItem) => {
  items.value.unshift({
    id: Date.now(),
    ...newItem,
    owner: { ...currentUser },
    createdAt: new Date().toISOString().split('T')[0]
  })
  ElMessage.success('发布成功！')
}

const addExchangeRequest = (request) => {
  exchangeRequests.value.unshift({
    id: Date.now(),
    ...request,
    requester: { id: currentUser.id, nickname: currentUser.nickname, avatar: currentUser.avatar },
    createdAt: new Date().toISOString().split('T')[0],
    status: 'pending'
  })
  ElMessage.success('交换请求已发送！对方会尽快看到~')
}

const myPublishedItems = computed(() => {
  return items.value.filter(item => item.owner.id === currentUser.id)
})

const myReceivedRequests = computed(() => {
  return exchangeRequests.value.filter(req => {
    const item = items.value.find(i => i.id === req.itemId)
    return item && item.owner.id === currentUser.id
  })
})

const mySentRequests = computed(() => {
  return exchangeRequests.value.filter(req => req.requester.id === currentUser.id)
})

const deleteItem = (itemId) => {
  const index = items.value.findIndex(i => i.id === itemId)
  if (index > -1) {
    items.value.splice(index, 1)
    ElMessage.success('物品已删除')
  }
}
</script>

<template>
  <div class="app-container">
    <header class="app-header">
      <div class="header-content">
        <div class="logo">
          <span class="logo-icon">🛒</span>
          <span class="logo-text">跳蚤市场</span>
        </div>
        <nav class="nav-tabs">
          <el-button 
            :type="currentTab === 'home' ? 'primary' : ''" 
            @click="currentTab = 'home'"
            plain
          >
            🏠 物品广场
          </el-button>
          <el-button 
            :type="currentTab === 'profile' ? 'primary' : ''" 
            @click="currentTab = 'profile'"
            plain
          >
            👤 个人中心
          </el-button>
        </nav>
      </div>
    </header>
    
    <main class="app-main">
      <div v-if="currentTab === 'home'" class="home-page">
        <div class="home-actions">
          <el-button type="success" size="large" @click="showPublishModal = true">
            ✨ 发布闲置
          </el-button>
        </div>
        <ItemList :items="items" @send-exchange="addExchangeRequest" />
      </div>
      
      <div v-else-if="currentTab === 'profile'" class="profile-page">
        <UserProfile 
          :current-user="currentUser"
          :my-items="myPublishedItems"
          :received-requests="myReceivedRequests"
          :sent-requests="mySentRequests"
          @delete-item="deleteItem"
        />
      </div>
    </main>
    
    <PublishModal 
      v-model:visible="showPublishModal"
      @submit="addItem"
    />
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  background-color: #f5f7fa;
}

.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.app-header {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  height: 64px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  gap: 8px;
  color: white;
  font-size: 22px;
  font-weight: bold;
}

.logo-icon {
  font-size: 28px;
}

.nav-tabs {
  display: flex;
  gap: 10px;
}

.app-main {
  flex: 1;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
  padding: 20px;
}

.home-page {
  width: 100%;
}

.home-actions {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 24px;
}

.profile-page {
  width: 100%;
}
</style>
