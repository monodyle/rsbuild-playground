<template>
  <div v-html="parsed" />
</template>

<script>
export default {
  name: 'Content',
  inheritAttrs: false,
  customOptions: {}
}
</script>

<script setup>
import rehypeShiki from '@shikijs/rehype'
import rehypeStringify from 'rehype-stringify'
import remarkParse from 'remark-parse'
import remarkRehype from 'remark-rehype'
import { unified } from 'unified'

const request = await fetch('/input.md')
const content = await request.text()

const parsed = await unified()
  .use(remarkParse)
  .use(remarkRehype)
  .use(rehypeShiki, {
    themes: {
      light: 'vitesse-light',
      dark: 'vitesse-dark',
    },
  })
  .use(rehypeStringify)
  .process(content)
</script>
