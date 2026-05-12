<script setup>
import { ref } from 'vue'

const props = defineProps({
  currentUser: {
    type: Object,
    required: true
  },
  myItems: {
    type: Array,
    default: () => []
  },
  receivedRequests: {
    type: Array,
    default: () => []
  },
  sentRequests: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['delete-item'])

const activeTab = ref('my-items')
</script>

<template>
  <div class="profile-container">
    <el-card class="user-info-card" shadow="never">
      <div class="user-info">
        <img :src="currentUser.avatar" class="avatar" />
        <div class="user-detail">
          <h2 class="nickname">{{ currentUser.nickname }}</h2>
          <p class="bio">{{ currentUser.bio }}</p>
        </div>
      </div>
    </el-card>

    <el-card class="content-card" shadow="never">
      <el-tabs v-model="activeTab">
        <el-tab-pane label="我发布的" name="my-items">
          <div v-if="myItems.length === 0" class="empty-state">
            <p>你还没有发布任何物品哦~</p>
          </div>
          <div v-else class="my-items-list">
            <div class="my-item" v-for="item in myItems" :key="item.id">
              <img :src="item.image" class="item-thumb" />
              <div class="item-info">
                <h4>{{ item.name }}</h4>
                <p class="item-desc">{{ item.description }}</p>
                <div class="item-tags">
                  <el-tag size="small" type="info">{{ item.condition }}</el-tag>
                  <el-tag 
                    size="small" 
                    :type="item.type === 'sale' ? 'success' : 'warning'"
                  >
                    {{ item.type === 'sale' ? '出售' : '交换' }}
                  </el-tag>
                </div>
              </div>
              <el-button 
                type="danger" 
                plain 
                size="small"
                @click="emit('delete-item', item.id)"
              >
                删除
              </el-button>
            </div>
          </div>
        </el-tab-pane>
        
        <el-tab-pane label="收到的请求" name="received">
          <div v-if="receivedRequests.length === 0" class="empty-state">
            <p>还没有收到交换请求~</p>
          </div>
          <div v-else class="requests-list">
            <div class="request-card" v-for="req in receivedRequests" :key="req.id">
              <div class="requester-info">
                <img :src="req.requester.avatar" class="requester-avatar" />
                <span class="requester-name">{{ req.requester.nickname }}</span>
              </div>
              <div class="request-content">
                <p class="request-item">关于：{{ req.itemName }}</p>
                <p class="request-message">{{ req.message }}</p>
                <span class="request-date">{{ req.createdAt }}</span>
              </div>
            </div>
          </div>
        </el-tab-pane>

        <el-tab-pane label="我发出的" name="sent">
          <div v-if="sentRequests.length === 0" class="empty-state">
            <p>你还没有发起过交换请求~</p>
          </div>
          <div v-else class="requests-list">
            <div class="request-card" v-for="req in sentRequests" :key="req.id">
              <div class="request-content">
                <p class="request-item">你请求：{{ req.itemName }}</p>
                <p class="request-message">{{ req.message }}</p>
                <el-tag size="small" type="primary">待对方回复</el-tag>
                <span class="request-date">{{ req.createdAt }}</span>
              </div>
            </div>
          </div>
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </div>
</template>

<style scoped>
.profile-container {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.user-info-card {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 16px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 20px;
}

.avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  border: 4px solid white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.user-detail {
  color: white;
}

.nickname {
  font-size: 24px;
  margin-bottom: 8px;
}

.bio {
  font-size: 14px;
  opacity: 0.9;
}

.content-card {
  border-radius: 12px;
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: #909399;
}

.my-items-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.my-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 12px;
  border: 1px solid #ebeef5;
  border-radius: 8px;
}

.item-thumb {
  width: 80px;
  height: 80px;
  object-fit: cover;
  border-radius: 6px;
}

.item-info {
  flex: 1;
}

.item-info h4 {
  font-size: 16px;
  color: #303133;
  margin-bottom: 4px;
}

.item-desc {
  font-size: 13px;
  color: #909399;
  margin-bottom: 8px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.item-tags {
  display: flex;
  gap: 8px;
}

.requests-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.request-card {
  padding: 16px;
  border: 1px solid #ebeef5;
  border-radius: 8px;
}

.requester-info {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}

.requester-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
}

.requester-name {
  font-weight: 500;
  color: #303133;
}

.request-item {
  font-size: 14px;
  color: #606266;
  margin-bottom: 8px;
}

.request-message {
  font-size: 14px;
  color: #303133;
  padding: 10px;
  background: #f5f7fa;
  border-radius: 6px;
  margin-bottom: 10px;
}

.request-date {
  font-size: 12px;
  color: #909399;
  float: right;
}
</style>
