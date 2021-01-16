<template>
  <div class="container column">
    <form class="card card-w30" @submit.prevent="addBlock">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="blockType">
          <option value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" rows="3" v-model.trim="blockValue"></textarea>
      </div>

      <button class="btn primary" :disabled="!checkLength">Добавить</button>
    </form>

    <div class="card card-w70">
      <component
          v-for="block in blocks"
          :key="block.id"
          :is="'app-' + block.type"
          :data="block"
          ></component>
      <h3 v-if="!blocks.length">Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>

  <div class="container">
    <p>
      <button class="btn primary" @click="loadComments">Загрузить комментарии</button>
    </p>
    <app-loader v-if="loading"></app-loader>
    <app-comments :comments="comments" v-if="comments.length"></app-comments>
  </div>
</template>

<script>
import AppComments from '@/components/AppComments';
import AppTitle from '@/components/AppTitle';
import AppAvatar from '@/components/AppAvatar';
import AppSubtitle from '@/components/AppSubtitle';
import AppText from '@/components/AppText';
import AppLoader from '@/components/AppLoader';
import axios from 'axios'

const firebase = 'https://vue3-with-http-default-rtdb.europe-west1.firebasedatabase.app/'

export default {
  data() {
    return {
      blockType: '',
      blockValue: '',
      blocks: [],
      comments: [],
      loading: false
    }
  },
  mounted() {
    this.loadBlocks()
  },
  methods: {
    async loadBlocks() {
      try {
        const {data} = await axios.get(firebase + 'resume.json')
        this.blocks = Object.keys(data).map(key => {
          return {
            id: key,
            ...data[key]
          }
        })
      } catch (e) {
        console.log('Нет добавленных блоков.')
      }
    },
    async addBlock() {
      const response = await axios.post(firebase + 'resume.json',{
        type: this.blockType,
        value: this.blockValue,
        sort: 0
      })
      this.loadBlocks()
      this.clearInput()
    },
    clearInput() {
      this.blockType = ''
      this.blockValue = ''
    },
    loadComments() {
      this.loading = true
      setTimeout(async () => {
        this.comments = (await axios.get('https://jsonplaceholder.typicode.com/comments?_limit=42')).data
        this.loading = false
      }, 1500)
    }
  },
  provide() {
    return {
      isLoading: this.loading
    }
  },
  computed: {
    checkLength() {
      return this.blockValue.length >= 3
    },
  },
  components: {AppText, AppSubtitle, AppAvatar, AppTitle, AppComments, AppLoader}
}
</script>

<style>
.avatar {
  display: flex;
  justify-content: center;
}

.avatar img {
  width: 150px;
  height: auto;
  border-radius: 50%;
}
</style>
