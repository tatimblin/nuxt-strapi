<template>
  <div class="blog">
    <h1>Blog</h1>
    <ul>
      <li v-for="post in filteredList" :key="post.id">
        <h2>{{ post.title }}</h2>
        <h3>{{ post.published }}</h3>
        <p>{{ post.body }}</p>
      </li>
    </ul>
  </div>
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
  created() {
    console.log(process.env.VUE_APP_STRAPI);
  },
  computed: {
    filteredList() {
      return this.posts.filter(post => {
        return post.title
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
        ...post
      })
    })
  }
}
</script>
