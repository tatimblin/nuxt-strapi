<template>
  <ul>
    <li v-for="post in filteredList" :key="post.id">
      hi
    </li>
  </ul>
</template>

<script>
import Strapi from 'strapi-sdk-javascript/build/main'
const apiUrl = process.env.API_URL || 'http://localhost:1337'
const strapi = new Strapi(apiUrl)
export default {
  data() {
    return {
      query: '',
    }
  },
  computed: {
    filteredList() {
      return this.posts.filter(post => {
        return post.name.toLowerCase().includes(this.query.toLowerCase())
      })
    },
    posts() {
      return this.$store.getters['posts/list']
    }
  },
  async fetch({ store }) {
    store.commit('posts/emptyList')
    const response = await strapi.request('post', '/graphql', {
      data: {
        query: `query {
          posts {
            id
            title
            published
            body
          }
        }`
      }
    })
    response.data.posts.forEach(post => {
      store.commit('posts/add', {
        id: post.id,
        ...restaurant
      })
    })
  }
}
</script>
