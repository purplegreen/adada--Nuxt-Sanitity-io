<template>
  <section>
    <h1>DADA</h1>

    <ul>
      <li v-for="post in posts" :key="post._id" class="">
        <h3>{{ post.title }}</h3>

        <img
          v-if="post.mainImage"
          class="mainImage"
          :src="imageUrlFor(post.mainImage).ignoreImageParams()"
        />

        <p class="block-content">
          <block-content :blocks="post.body" />
        </p>
      </li>
    </ul>
  </section>
</template>

<script>
import imageUrlBuilder from '@sanity/image-url'
import BlockContent from 'sanity-blocks-vue-component'
import sanity from '../sanity'

const imageBuilder = imageUrlBuilder(sanity)
const query = `*[_type == "post" ] | order(releaseDate desc)
{
  _id,
  title,
  mainImage,
  imageUrl,
  createdAt,
  releaseDate,
  body
}[0...29]`

export default {
  name: 'Posts',
  components: {
    BlockContent,
  },
  data() {
    return {
      loading: true,
      posts: [],
    }
  },
  watch: {
    $route: 'fetchData',
  },
  created() {
    this.fetchData()
  },
  mounted() {},
  methods: {
    imageUrlFor(source) {
      return imageBuilder.image(source)
    },
    fetchData() {
      this.error = this.post = null
      this.loading = true
      sanity.fetch(query).then(
        (posts) => {
          this.posts = posts
          // this.loading = false
        },
        (error) => {
          this.error = error
        }
      )
      // .then(() => {
      //   this.startAnimation()
      // })
    },
  },
  head() {
    return {
      title: 'Listing',
    }
  },
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
