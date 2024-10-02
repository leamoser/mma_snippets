# HS24 - Headless CMS
> Diese Snippets werden im Unterricht gebraucht. 

**1** package.json
```text
NODE_TLS_REJECT_UNAUTHORIZED=0 nuxt dev --https --ssl-cert localhost.pem --ssl-key localhost-key.pem
```

**2** Terminal
```shell
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init
```

**3** nuxt.config.js
```javascript
postcss: {
    plugins: {
        tailwindcss: {},
        autoprefixer: {}
    }
}
```

**4** tailwind.config.js
```javascript
content: [
    "./components/**/*.{js,vue,ts}",
    "./layouts/**/*.vue",
    "./pages/**/*.vue",
    "./plugins/**/*.{js,ts}",
    "./app.vue",
    "./error.vue",
    "./storyblok/**/*.vue",
]
```

**5** main.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

**6** nuxt.config.js
```javascript
css: ['~/assets/css/main.css']
```

**7** nuxt.config.js
```javascript
modules: [['@storyblok/nuxt', { accessToken: process.env.STORYBLOK_KEY }]],
runtimeConfig: {
    public: {
      storyblok_version: process.env.STORYBLOK_VERSION
    }
}
```

**8** index.vue
```vue
<script setup>
  const config = useRuntimeConfig()
  const story = await useAsyncStoryblok('home', { version: config.public.storyblok_version })
</script>

<template>
  <mark>index.vue</mark>
</template>
```
**9** index.vue
```vue
<p class="text-green-600 text-2xl">index.vue</p>
<StoryblokComponent v-if="story" :blok="story.content" />
```

**10** Page.vue
```vue
<script setup>
const props = defineProps({
  blok: { required: true, type: Object }
})
</script>

<template>
  <main v-editable="blok">
    <p class="text-red-500 text-2xl">Page.vue</p>
    <StoryblokComponent v-for="blok in blok.components" :key="blok._uid" :blok="blok" />
  </main>
</template>
```

**11** Hero.vue
```vue
<script setup>
const props = defineProps({
  blok: { required: true, type: Object }
})
</script>

<template>
  <section v-editable="blok">
    <p class="text-blue-700 text-2xl">Hero.vue</p>
  </section>
</template>
```

**12** OverviewTestimonials.vue
```vue
<script setup>
const props = defineProps({
  blok: { required: true, type: Object }
})
</script>

<template>
  <section v-editable="blok">
    <p class="text-blue-700 text-2xl">OverviewTestimonials.vue</p>
  </section>
</template>
```

**13** index.vue
```javascript
const story = await useAsyncStoryblok(
      'home',
      {
        version: config.public.storyblok_version,
        resolve_relations: 'overviewTestimonials.testimonials'
      },
      {
        resolveRelations: ['overviewTestimonials.testimonials'],
      }
  )
```

**14** Testimonial.vue
```vue
<script setup>
const props = defineProps({
  blok: { required: true, type: Object }
})
</script>

<template>
  <article class="w-full flex flex-col gap-4 items-center">
    <img class="rounded-full size-60" :src="blok.image.filename" :alt="blok.image.alt" />
    <div class="flex flex-col gap-2 *:text-center *:text-black *:font-inter *:font-normal">
      <h2 class="text-2xl">{{ blok.name }}</h2>
      <p class="text-base">{{ blok.statement }}</p>
    </div>
  </article>
</template>
```

**15** Hero.vue
```vue
<script setup>
const props = defineProps({
  blok: { required: true, type: Object }
})
</script>

<template>
  <section v-editable="blok" class="bg-red-500 py-52 *:text-center flex flex-col items-center justify-center px-12 gap-3">
    <h1 class="font-inter text-white text-8xl italic font-black uppercase max-w-3xl leading-none">
      {{ blok.title }}
    </h1>
    <p class="font-inter text-red-200 text-3xl font-normal max-w-md">{{ blok.subtitle }}</p>
  </section>
</template>
```

**16** OverviewTestimonials.vue
```vue
<script setup>
import Testimonial from "~/components/Testimonial.vue";
const props = defineProps({
  blok: { required: true, type: Object }
})
</script>

<template>
  <section v-editable="blok" class="bg-yellow-300 py-20 px-12 flex flex-col gap-14">
    <h1 class="text-center text-black font-black italic font-inter uppercase text-5xl">
      {{ blok.title }}
    </h1>
    <div class="flex items-center gap-10">
      <Testimonial v-for="blok in blok.testimonials" :key="blok.uuid" :blok="blok.content" />
    </div>
  </section>
</template>
```

**17** main.css
```css
@import url('https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap');
```

**18** tailwind.config.js
```javascript
theme: {
    extend: {
      fontFamily: {
        inter: ['"Inter"']
      }
    }
},
```

**S19** Header.vue
```vue
<script setup>
const storyblokApi = useStoryblokApi()
const config = useRuntimeConfig()
const { data } = await storyblokApi.get('cdn/stories/globals', {
  version: config.public.storyblok_version
})
const logo = data.story.content.logo;
</script>

<template>
  <header class="w-full fixed p-12 pointer-events-none flex">
    <NuxtLink class="pointer-events-auto" to="/">
      <img class="size-40" :src="logo.filename" :alt="logo.alt">
    </NuxtLink>
  </header>
</template>
```
