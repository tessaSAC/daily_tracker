<script>
import {
  Check,
  Close,
  Minus,
  Plus,
} from '@element-plus/icons-vue'

export default {
  data: _ => ({
    Check,
    Close,
    Minus,
    Plus,

    count: 0,
    key: 'dailyCount2897129387491082',
    showStartingCountInput: false,
    startingCount: 0,
  }),

  created() {
    const countStr = localStorage.getItem(this.key)

    if (countStr) {
      const countObj = JSON.parse(countStr)
      const now = new Date()

      if (now.getTime() > countObj.expiry) {
        localStorage.removeItem(this.key)
        return
      }

      this.count = countObj.count
    }
  },

  watch: {
    count(newCount, oldCount) {
      this.saveCount()
    }
  },

  methods: {
    decreaseCount() {
      this.count = this.count === 0
        ? 0
        : this.count - 1
    },

    increaseCount() {
      ++this.count
    },

    saveCount() {
      // https://www.sohamkamani.com/javascript/localstorage-with-ttl-expiry
      const now = new Date()
      const ttl = 172800000 // 48 hours

      const countObj = {
        count: this.count,
        expiry: now.getTime() + ttl
      }

      localStorage.setItem(this.key, JSON.stringify(countObj))
    },

    setStartingCount() {
      this.count = this.startingCount
      this.toggleStartingCountInput()
    },

    toggleStartingCountInput() {
      this.startingCount = 0
      this.showStartingCountInput = !this.showStartingCountInput
    }
  }
}
</script>

<template>
  <el-menu :default-active="activeIndex" mode="horizontal" :ellipsis="false" class="menu" @select="handleSelect">
    <div class="flex-grow" />

    <template v-if="showStartingCountInput">
      <el-input-number class="startingCounter" v-if="showStartingCountInput" v-model="startingCount" />

      <el-button-group>
        <el-button type="success" :icon="Check" circle plain @click="setStartingCount" />
        <el-button type="danger" :icon="Close" circle plain @click="toggleStartingCountInput" />
      </el-button-group>
    </template>

    <el-button v-else round @click="toggleStartingCountInput">Set starting count</el-button>
  </el-menu>

  <h1 class="title">daily tracker</h1>
  <el-row class="counter">
    <el-text class="count">{{ this.count }}</el-text>
    <div>
      <el-button class="counterButton" size="large" type="danger" :icon="Minus" circle plain @click="decreaseCount" />
      <el-button class="counterButton" size="large" type="success" :icon="Plus" circle plain @click="increaseCount" />
    </div>
  </el-row>
</template>

<style scoped>
.title {
  margin-bottom: 0;
  font-size: 5vh;
  color: grey;
}

.counter {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 auto;

  .count {
    margin-bottom: 14vh;
    height: 40vh;
    font-size: 40vh;
    font-weight: bolder;
    color: darkslategrey;
  }

  .counterButton {
    height: 5vh;
    width: 5vh;
    font-size: 3vh;
    margin: 0 2vh;
  }
}

.startingCounter {
  margin-right: 16px;
}

.flex-grow {
  flex-grow: 1;
}

.menu {
  display: flex;
  align-items: center;
  height: 80px;
}
</style>
