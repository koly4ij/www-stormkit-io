<template>
  <div class="text-blue-50">
    <div class="page px-3 md:px-0">
      <div
        class="flex flex-col md:flex-row bg-white rounded-lg shadow m-auto mt-12"
      >
        <blog-menu :pages="pages" class="md:mt-6" />
        <div class="md:p-6 text-sm leading-relaxed md:w-3/4">
          <article class="mb-8 p-4 bg-gray-90 rounded-lg markdown">
            <h1 class="font-bold text-3xl mt-4 mb-8">
              {{ page.title }}
            </h1>
            <nuxt-content :document="page" tag="main" class="blog-post" />
          </article>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import BlogMenu from '~/components/molecules/BlogMenu.vue'

export default {
  components: {
    BlogMenu,
  },

  async asyncData({ $content, params, error }) {
    const slug = params.slug || 'index'
    const page = await $content(`blog/${slug}`)
      .fetch()
      .catch(() => {
        error({ statusCode: 404, message: 'Page not found' })
      })

    const pages = await $content('blog')
      .sortBy('updatedAt', 'desc')
      .fetch()
      .catch(() => {
        error({ statusCode: 404, message: 'Page not found' })
      })

    return {
      page,
      pages,
    }
  },

  head() {
    const title = `Stormkit - ${this.page.title}`

    return {
      title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.page.description,
        },
        // Open Graph
        { hid: 'og:title', property: 'og:title', content: title },
        {
          hid: 'og:description',
          property: 'og:description',
          content: this.page.description,
        },
        {
          hid: 'og:image',
          property: 'og:image',
          content: 'https://stormkit.io/stormkit-logo-rect.png',
        },
        // Twitter Card
        {
          hid: 'twitter:card',
          name: 'twitter:card',
          content: 'summary',
        },
        {
          hid: 'twitter:title',
          name: 'twitter:title',
          content: title,
        },
        {
          hid: 'twitter:description',
          name: 'twitter:description',
          content: this.page.description,
        },
        {
          hid: 'twitter:site',
          name: 'twitter:site',
          content: '@stormkit-io',
        },
        {
          hid: 'twitter:creator',
          name: 'twitter:creator',
          content: '@savasvedova',
        },
      ],
    }
  },
}
</script>
