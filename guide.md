# Vue install guide

## Contents
***is optional**

- [x] [vsc extensions](#extensions)
- [x] [create vue](#create-vue)
- [x] [prettier](#prettier)
- [x] [.env files](#env-files)
- [x] [typescript](#typescript)
- [x] [plugins](#plugins)
- [x] [webfonts](#webfonts)*
- [x] [sass](#sass)
- [ ] [axios](#axios)
- [x] [example files](#example-files)
  - [x] [router](#router)
  - [x] [transitions](#transitions)*
- [x] [vuetify](#vuetify)*
- [x] [ant design](#ant-design)*
- [x] [tailwind css](#tailwind-css)*
- [x] [pinia](#data-store)*
- [x] [i18n](#i18n)*
- [x] [vue query](#vue-query)*
  - [x] [rest]()
  - [ ] [graphql]()
- [x] [msw](#msw)*
  - [x] [rest]()
  - [ ] [graphql]()
- [ ] authentication
- [x] [unit tests](#unit-tests)*
  - [x] [with msw](#with-msw)
- [x] [e2e tests](#e2e-tests)*
  - [x] [with msw](#with-msw-1)
- [x] [storybook](#storybook)*
  - [x] [plugins](#plugins-1)
  - [x] [router](#router-1)
  - [x] [pinia](#pinia)
  - [x] [i18n](#i18n-1)
  - [x] [vue query](#vue-query-1)
  - [x] [msw](#msw-1)
  - [x] [tests](#e2e-tests-1)
  - [ ] authentication
  - [x] [other addons](#other-addons)
- [x] [other libraries](#other-libraries)
- [x] [unused files](#unused-files-that-can-be-deleted)
- [x] [folder structure](#folder-structure)

## Todos
- [ ] folder and file naming
- [ ] write better test and align with storybook
- [ ] access playwright with sophos enabled
- [ ] test build
- [ ] import .mocks data or hardcode in test
- [ ] prettier differences with autoformat
- [ ] axios setup
- [ ] authentication examples
- [ ] graphql
- [ ] storybook explanations in readme
- [ ] fix tsconfig "class" error when adding .storybook to the includes

## VSC extensions
- [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar)
- [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin)
- [Vue devtools for chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd?hl=en)

## Create Vue
[Create vue cli](https://github.com/vuejs/create-vue)

```sh
npm create vue@3
```

Use the following settings:
- Add TypeScript? **Yes**
- Add JSX Support? **Yes***
- Add Vue Router for Single Page Application development? **Yes**
- Add Pinia for state management? **Yes***
- Add Vitest for Unit Testing? **Yes***
- Add an End-to-End Testing Solution? » - Use arrow-keys. Return to submit. **Playwright***
- Add ESLint for code quality? **Yes**
- Add Prettier for code formatting? **Yes**

```sh
cd project-name
```
```sh
npm install
```
```sh
npm run lint
```
```sh
npm run dev
```

## Prettier
[Prettier](https://prettier.io/)

Update `.prettierrc.json`
```json
{
  "singleQuote": true,
  "semi": false,
  "trailingComma": "none",
  "printWidth": 120
}
```

## .env files
Add the following to `.gitignore`
```diff
+ # local env files
+ .env
```
Create `.env`
```
VITE_API_HOST=https://reqres.in/api/
```
Create `.env.example`
```
VITE_API_HOST=api base path
```

## Typescript
[Typescript](https://www.typescriptlang.org/)

Create `src/models/examples.types.ts`
```ts
export type Example = {
  id: number
  name: string
  year: number
  color: string
  pantone_value: string
}

export type ExamplesGetResponse = {
  page: number
  per_page: number
  total: number
  total_pages: number
  data: Example[]
}
```

## Plugins
Create `src/plugins/index.ts`
```ts
// Plugins
import router from '@/router'
import { createPinia } from 'pinia'

// Styles
import '@/assets/main.css'

// Types
import type { App } from 'vue'

export function registerPlugins(app: App) {
  app.use(router).use(createPinia())
}
```

Update `src/main.ts`
```diff
import { createApp } from 'vue'
- import { createPinia } from 'pinia'

import App from './App.vue'
- import router from './router'
+ import { registerPlugins } from '@/plugins'

- import './assets/main.css'

const app = createApp(App)

- app.use(createPinia())
- app.use(router)
+ registerPlugins(app)

app.mount('#app')
```

## Webfonts
[Webfonts](https://www.npmjs.com/package/webfontloader)

```sh
yarn add webfontloader; yarn add -D @types/webfontloader
```

Create `src/plugins/webfontloader.ts`
```ts
/**
 * plugins/webfontloader.ts
 *
 * webfontloader documentation: https://github.com/typekit/webfontloader
 */

export async function loadFonts() {
  const webFontLoader = await import(
    /* webpackChunkName: "webfontloader" */ 'webfontloader'
  )

  webFontLoader.load({
    google: {
      families: ['Roboto:100,300,400,500,700,900&display=swap']
    }
  })
}
```

Update `src/plugins/index.ts`
```diff
// Plugins
+ import { loadFonts } from './webfontloader'

export function registerPlugins(app: App) {
+  loadFonts()
```

## Sass
[Sass](https://sass-lang.com/)

```sh
yarn add -D sass sass-loader
```

Create `src/assets/styles/styles.scss`
```scss
  @import 'main.css';
```

Move the following files

`src/assets/base.css` => `src/assets/styles/base.css`

`src/assets/main.css` => `src/assets/styles/main.css`

Update `src/plugins/index.ts`
```diff
- import '@/assets/main.css'
+ import '@/assets/styles/styles.scss'
```

## Axios
[Axios](https://github.com/axios/axios)

```sh
yarn add axios
```

## Example files
Create `src/components/Example/ExampleComponent.vue`
```vue
<template>
  <div>
    <h2>
      {{ example.name }}
    </h2>
    <p>
      <span :style="{ backgroundColor: example.color }" class="color" />{{
        example.pantone_value
      }}
    </p>
  </div>
</template>

<script setup lang="ts">
import type { PropType } from 'vue'
import type { Example } from '@/models/examples.types'

defineProps({
  example: {
    type: Object as PropType<Example>,
    required: true
  }
})
</script>

<style lang="scss" scoped>
.color {
  display: inline-block;
  width: 1em;
  height: 1em;
  margin-right: 0.5em;
}
</style>
```
Create `src/views/ExampleView/ExampleView.vue`
```vue
<template>
  <div>
    <h1>Example</h1>
    <h2>Pinia</h2>
    <div>count: {{ counter.count }}</div>
    <button @click="counter.increment">Increment</button>
    <h2>Api call</h2>
    <div v-if="query.isLoading">Loading...</div>
    <div v-else-if="query.isError">An error has occurred: {{ query.error }}</div>
    <div v-if="query.data">
      <ExampleComponent v-for="example in query.data" :key="example.id" :example="example" class="examples" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { onBeforeMount, ref } from 'vue'
import axios from 'axios'
import type { ExamplesGetResponse } from '@/models/examples.types'
import ExampleComponent from '@/components/Example/ExampleComponent.vue'
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()

const query = ref()

async function getExample() {
  try {
    query.value = { isLoading: true }
    const { data } = await axios.get<ExamplesGetResponse>(`${import.meta.env.VITE_API_HOST}examples`)
    query.value = data
    return data
  } catch (error) {
    if (axios.isAxiosError(error)) {
      query.value = { isError: error.message }
      console.log('error message: ', error.message)
      return error.message
    } else {
      query.value = { isError: error }
      console.log('unexpected error: ', error)
      return 'An unexpected error occurred'
    }
  }
}

onBeforeMount(() => {
  getExample()
})
</script>
```

### Router
[Vue router](https://router.vuejs.org/)

Update `src/router/index.ts`
```diff
[
-  {
-   path: '/about',
-   name: 'about',
-   // route level code-splitting
-   // this generates a separate chunk (About.[hash].js) for this route
-   // which is lazy-loaded when the route is visited.
-   component: () => import('../views/AboutView.vue')
-  }
+  {
+   path: '/example',
+   name: 'example',
+   component: () => import('@/views/ExampleView/ExampleView.vue')
+  }
]
```

Update `src/App.vue`
```diff
- <script setup lang="ts">
- import { RouterLink, RouterView } from 'vue-router'
- import HelloWorld from './components/HelloWorld.vue'
- </script>
-
-<template>
- <header>
-   <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" />
-
-   <div class="wrapper">
-     <HelloWorld msg="You did it!" />
-
-     <nav>
-       <RouterLink to="/">Home</RouterLink>
-       <RouterLink to="/about">About</RouterLink>
-     </nav>
-   </div>
- </header>
-
- <RouterView />
- </template>
+<template>
+ <header>
+   <div class="wrapper">
+     <nav>
+       <router-link to="/">Home</router-link>
+       <router-link to="/example">Example</router-link>
+     </nav>
+   </div>
+ </header>
+ <div class="fade_container">
+   <router-view v-slot="{ Component }">
+     <transition name="fade">
+       <component :is="Component" :key="$route.path" />
+     </transition>
+   </router-view>
+ </div>
+</template>
+
+<script setup lang="ts" />
```

### Transitions
[Router transitions](https://router.vuejs.org/guide/advanced/transitions.html)

Create `src/assets/styles/_transitions.scss`
```scss
.fade_container {
  display: flex;

  > * {
    width: 100%;
    flex-shrink: 0;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-active:not(:first-child) {
  margin-left: -100%;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```
Update `src/assets/styles/styles.scss`
```diff
+ @import 'transitions';
```
## Vuetify
[Vuetify](https://next.vuetifyjs.com/en/)

```sh
yarn add vuetify@next @mdi/font; yarn add -D vite-plugin-vuetify
```

[Theme](https://next.vuetifyjs.com/en/features/theme/)

Create `src/plugins/vuetify.ts`
```ts
/**
 * plugins/vuetify.ts
 *
 * Framework documentation: https://vuetifyjs.com`
 */

// Styles
import '@mdi/font/css/materialdesignicons.css'
import 'vuetify/styles'

// Composables
import { createVuetify } from 'vuetify'

// https://vuetifyjs.com/en/introduction/why-vuetify/#feature-guides
export default createVuetify({
  theme: {
    themes: {
      light: {
        colors: {
          primary: '#1867C0',
          secondary: '#5CBBF6'
        }
      }
    }
  }
})
```

Update `src/plugins/index.ts`
```diff
+ import vuetify from './vuetify'
import router from '@/router'

app
+ .use(vuetify)
```

Replace `src/App.vue`
```ts
<template>
  <v-app>
    <v-toolbar>
      <v-btn :to="{ name: 'home' }" exact>Home</v-btn>
      <v-btn :to="{ name: 'example' }">Example</v-btn>
    </v-toolbar>
    <v-main class="fade_container">
      <router-view v-slot="{ Component }">
        <transition name="fade">
          <component :is="Component" :key="$route.path" />
        </transition>
      </router-view>
    </v-main>
  </v-app>
</template>

<script setup lang="ts" />
```

Update `vite.config.ts`
```diff
import vueJsx from '@vitejs/plugin-vue-jsx'
+ import vuetify, { transformAssetUrls } from 'vite-plugin-vuetify'

plugins: [
- vue(),
+ vue({
+   template: { transformAssetUrls }
+ }),
  vueJsx(),
+ vuetify({
+   autoImport: true
+ })
],
```

Update `src/views/ExampleView/ExampleView.vue`
```diff
- <button @click="counter.increment">Increment</button>
+ <v-btn @click="counter.increment">Increment</v-btn>
```

## Ant design
[Vue Ant design](https://antdv.com/docs/vue/introduce)
```sh
yarn add ant-design-vue; yarn add -D less
```

Update `src/plugins/index.ts`
```diff
+ import 'ant-design-vue/dist/antd.less'
import '@/assets/styles/styles.scss'
```

[Theme](https://2x.antdv.com/docs/vue/customize-theme)

Update `vite.config.ts`
```diff
+ css: {
+  preprocessorOptions: {
+   less: {
+     modifyVars: {
+       'primary-color': '#1DA57A'
+     },
+     javascriptEnabled: true
+   }
+ }
+}
```

### Tailwind css
[Tailwind css](https://v2.tailwindcss.com/docs/guides/vue-3-vite)

```sh
yarn add -D tailwindcss@latest postcss@latest autoprefixer@latest
```

```sh
npx tailwindcss init -p
```

Create `src/assets/styles/tailwind.css`
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Update `src/plugins/index.ts`
```diff
+ import '@/assets/styles/tailwind.css'
import '@/assets/styles/styles.scss'
```

Update `postcc.config.js`
```diff
+ /* eslint-env node */
module.exports = {
```

Update `tailwind.config.js`
```diff
+ /* eslint-env node */
/** @type {import('tailwindcss').Config} */

- content: [],
+ content: ['./index.html', './src/**/*.{vue,js,ts,jsx,tsx}'],
```

## Data store
[Pinia](https://pinia.vuejs.org/)

## i18n
[Vue i18n](https://vue-i18n.intlify.dev/)
```sh
yarn add vue-i18n@9
```

Update `tsconfig.app.json`
```diff
  "include": [
+   "src/**/*.json",
  ],
```
Create `src/locales/en.json`
```json
{
  "header": {
    "home": "Home",
    "example": "Example"
  }
}
```
Create `src/locales/ja.json`
```json
{
  "header": {
    "home": "トップ",
    "example": "例"
  }
}
```
Create `src/plugins/i18n.ts`
```ts
import { createI18n } from 'vue-i18n'
import en from '@/locales/en.json'
import ja from '@/locales/ja.json'

const i18n = createI18n({
  locale: 'en',
  fallbackLocale: 'en',
  messages: {
    en,
    ja
  }
})

export default i18n
```
Update `src/plugins/index.ts`
```diff
import { createPinia } from 'pinia'
+ import i18n from "./i18n"

    .use(pinia)
+   .use(i18n)
```
Update `src/App.vue`
### Without Vuetify
```diff
      </nav>
+     <select v-model="$i18n.locale">
+       <option v-for="locale in $i18n.availableLocales" :key="`locale-${locale}`" :value="locale">
+         {{ locale }}
+       </option>
+     </select>
    </div>
```
### With Vuetify
```diff
  <v-btn :to="{ name: 'example' }">{{ $t('header.example') }}</v-btn>
+ <v-spacer></v-spacer>
+ <v-select v-model="$i18n.locale" :items="$i18n.availableLocales" label="i18n" density="compact"></v-select>
```
## Vue Query
[Vue query](https://vue-query.vercel.app/)

```sh
yarn add @tanstack/vue-query
```

Update `src/plugins/index.ts`
```diff
import { createPinia } from 'pinia'
+ import { VueQueryPlugin } from '@tanstack/vue-query'

    .use(pinia)
+   .use(VueQueryPlugin)
```

Replace `src\views\ExampleView\ExampleView.vue`
```vue
<template>
  <div>
    <h1>Example</h1>
    <h2>Pinia</h2>
    <div>count: {{ counter.count }}</div>
    <button @click="counter.increment">Increment</button>
    <h2>Api call</h2>
    <div v-if="query.isLoading">Loading...</div>
    <div v-else-if="query.isError">An error has occurred: {{ query.error }}</div>
    <div v-else-if="query.data">
      <ExampleComponent v-for="example in query.data.data" :key="example.id" :example="example" class="examples" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { onBeforeMount, ref } from 'vue'
import axios from 'axios'
import { useQuery } from '@tanstack/vue-query'
import type { ExamplesGetResponse } from '@/models/examples.types'
import ExampleComponent from '@/components/Example/ExampleComponent.vue'
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()

const query = ref()

const exampleFetch = async (): Promise<ExamplesGetResponse[]> => {
  try {
    const response = await axios.get(`${import.meta.env.VITE_API_HOST}examples`)
    return response.data
  } catch (error) {
    console.error(error)
    return Promise.reject(error)
  }
}

onBeforeMount(() => {
  query.value = useQuery({
    queryKey: ['exampleFetch'],
    queryFn: exampleFetch
  })
})
</script>
```
With Vuetify, update `src/views/ExampleView/ExampleView.vue`
```diff
- <button @click="counter.increment">Increment</button>
+ <v-btn @click="counter.increment">Increment</v-btn>
```

## MSW
[MSW](https://mswjs.io/)

```sh
yarn add -D msw
```

Update `tsconfig.app.json`
```diff
{
  "include": [
+   ".mocks/**/*",
+   ".mocks/**/*.json"
  ]

  "compilerOptions": [
    "paths": [
+     "@mocks/*": [
+       ".mocks/*"
+     ]
    ]
  ],
}
```

Update `vite.config.ts`
```diff
{
  resolve: [
    alias: [
+     '@mocks': fileURLToPath(new URL('./.mocks', import.meta.url))
    ]
  ]
}
```

Create `.mocks/api/examples/examples.json`
```json
{
  "page": 2,
  "per_page": 6,
  "total": 12,
  "total_pages": 2,
  "data": [
    {
      "id": 7,
      "name": "sand dollar",
      "year": 2006,
      "color": "#DECDBE",
      "pantone_value": "13-1106"
    },
    {
      "id": 8,
      "name": "chili pepper",
      "year": 2007,
      "color": "#9B1B30",
      "pantone_value": "19-1557"
    },
    {
      "id": 9,
      "name": "blue iris",
      "year": 2008,
      "color": "#5A5B9F",
      "pantone_value": "18-3943"
    },
    {
      "id": 10,
      "name": "mimosa",
      "year": 2009,
      "color": "#F0C05A",
      "pantone_value": "14-0848"
    },
    {
      "id": 11,
      "name": "turquoise",
      "year": 2010,
      "color": "#45B5AA",
      "pantone_value": "15-5519"
    },
    {
      "id": 12,
      "name": "honeysuckle",
      "year": 2011,
      "color": "#D94F70",
      "pantone_value": "18-2120"
    }
  ]
}
```

Create `.mocks/api/examples/examples.ts`
```ts
import { rest } from 'msw'
import type { PathParams } from 'msw'

import type { ExamplesGetResponse } from '@/models/examples.types'

import examples from './examples.json'

export const examplesGetHandler = rest.get<
  object,
  PathParams,
  ExamplesGetResponse
>(`${import.meta.env.VITE_API_HOST}examples`, (req, res, ctx) =>
  res(ctx.status(200), ctx.delay(500), ctx.json(examples))
)

export default examplesGetHandler
```

Create `.mocks/handlers.ts`
```ts
import { examplesGetHandler } from './api/examples/examples'

// List of api mocks to always load
export const handlers = [examplesGetHandler]

// List of api mocks to only load if the VITE_LOCAL_MOCKS env is true
export const localHandlers = []

export const allHandlers = [...handlers, ...localHandlers]

export default handlers
```

### Start worker pattern 1
*This follows this [vite example](https://chaika.hatenablog.com/entry/2022/05/25/083000). However the delay in starting msw might cause the first api calls to not be intercepted.*

Create `.mocks/browser.ts`
```ts
import { setupWorker } from 'msw'

import { handlers, allHandlers } from '@mocks/handlers'

const workerHandlers = import.meta.env.VITE_LOCAL_MOCKS === 'true' ? allHandlers : handlers
export const worker = setupWorker(...workerHandlers)
```

Update `src/main.ts`

```diff
+ if (process.env.NODE_ENV === 'development') {
+ (async () => {
+   const { worker } = await import('@mocks/browser')
+   worker.start({ onUnhandledRequest: 'bypass' })
+ })()
+ }
```

### Start worker pattern 2
Update `src/main.ts`
```diff
+ import { setupWorker } from 'msw'
+ import { handlers, allHandlers } from '@mocks/handlers'

+ if (process.env.NODE_ENV === 'development') {
+ let workerHandlers = handlers
+ if (import.meta.env.VITE_LOCAL_MOCKS) {
+   workerHandlers = allHandlers
+ }
+ setupWorker(...workerHandlers).start({ onUnhandledRequest: 'bypass' })
+}
```

Update `.env`
```diff
+ VITE_LOCAL_MOCKS=false
```

Create `.env.msw`
```
VITE_API_HOST=https://reqres.in/api/
VITE_LOCAL_MOCKS=true
```
Update `.env.example`
```diff
VITE_API_HOST=api base path
+ VITE_LOCAL_MOCKS=always use mocks when doing api calls
```
Update `.gitignore`
```diff
.env
+ .env.msw
```

```sh
npx msw init public/ --save
```

Update `package.json`
```diff
{
  "scripts": {
+   "msw": "vite --mode msw",
  }
}
```
*Launching with `yarn msw` will always use the mocks for api calls*


## Unit tests
[Vue test utils](https://v1.test-utils.vuejs.org/)

[Vitest](https://vitest.dev/)

Create `src/components/Example/ExampleComponent.spec.ts`
```ts
import { describe, it, expect } from 'vitest'

import { mount } from '@vue/test-utils'
import ExampleComponent from './ExampleComponent.vue'

describe('ExampleComponent', () => {
  it('renders properly', () => {
    const wrapper = mount(ExampleComponent, {
      props: {
        example: {
          id: 7,
          name: 'sand dollar',
          year: 2006,
          color: '#DECDBE',
          pantone_value: '13-1106'
        }
      }
    })
    expect(wrapper.text()).toContain('sand dollar13-1106')
  })
})
```

### With MSW
Update `src/components/Example/ExampleComponent.spec.ts`
```diff
+ import examples from '@mocks/api/examples/examples.json'

-   example: {
-     id: 1,
-     name: 'cerulean',
-     year: 2000,
-     color: '#98B2D1',
-     pantone_value: '15-4020'
-   }
+   example: examples.data[0]

- expect(wrapper.text()).toContain('sand dollar13-1106')
+ expect(wrapper.text()).toContain(`${examples.data[0].name}${examples.data[0].pantone_value}`)
```

## e2e tests
[Playwright](https://playwright.dev/)

```sh
npx playwright install
```

delete `e2e/tsconfig.json`

Update `tsconfig.app.json`
```diff
  "include": [
+   "e2e/**/*",
+   "e2e/**/*.vue"
  ],
```

Create `e2e/example/ExampleFlow.spec.ts`
```ts
import { test, expect } from '@playwright/test'

// See here how to get started:
// https://playwright.dev/docs/intro
test('visits the app root url', async ({ page }) => {
  await page.goto('/example')
  await expect(page.locator('div.examples h2').first()).toHaveText('sand dollar')
})
```
### With MSW
Update `e2e/example/ExampleFlow.spec.ts`
```diff
+ import examples from '@mocks/api/examples/examples.json'

- await expect(page.locator('div.examples h2').first()).toHaveText('sand dollar')
+ await expect(page.locator('div.examples h2').first()).toHaveText(`${examples.data[0].name}`)
```

## Storybook
[Storybook](https://storybook.js.org/)

[Storybook 7 を Vue 3 + TypeScript ではじめよう！](https://zenn.dev/sa2knight/books/storybook-7-with-vue-3)

storybook 7+ is necessary, if it's not released yet use
```sh
npx sb@next init
```
otherwise use
```sh
npx storybook init
```
- Ok to proceed? (y) **y**
- Do you want to run the 'eslintPlugin' migration on your project? **y**

Update `tsconfig.app.json`
```diff
{
  "include": [
+   ".storybook/**/*",
  ]
  "compilerOptions": [
    "paths": [
+     "@stb/*": [
+       ".storybook/*"
+     ]
    ]
  ],
}
```
*In case "class" in vue templates throws an error, also add the following`*
```diff
{
  "compilerOptions": {
    "types": [
+     "vite/client"
    ]
  }
}
```

Update `vite.config.ts`
```diff
resolve: {
    alias: {
+     '@stb': fileURLToPath(new URL('./.storybook', import.meta.url))
    }
  },
```

Update `.storybook/main.ts`
```diff
- import { StorybookConfig } from '@storybook/vue3-vite'
+ import type { StorybookConfig } from '@storybook/vue3-vite'
```

Create `src/components/Example/ExampleComponent.stories.ts`
```ts
import type { Meta, StoryObj } from '@storybook/vue3'

import ExampleComponent from './ExampleComponent.vue'

const meta: Meta<typeof ExampleComponent> = {
  /* 👇 The title prop is optional.
   * See https://storybook.js.org/docs/7.0/vue/configure/overview#configure-story-loading
   * to learn how to generate automatic titles
   */
  title: 'Examples/Component',
  component: ExampleComponent,
  render: (args: any) => ({
    components: { ExampleComponent },
    setup() {
      return { args }
    },
    template: '<example-component :example="args.example" />'
  }),
  tags: ['autodocs']
}

export default meta
type Story = StoryObj<typeof ExampleComponent>

export const Default: Story = {
  args: {
    example: {
      id: 7,
      name: 'sand dollar',
      year: 2006,
      color: '#DECDBE',
      pantone_value: '13-1106'
    }
  }
}
```

### Plugins
Modify `.storybook/preview.ts`
```diff
+ import { setup } from '@storybook/vue3'
+ import { registerPlugins } from '@/plugins'
+ 
+ setup((app) => {
+   // Registers your app's plugins into Storybook
+   registerPlugins(app)
+ });

+ export const decorators = [
+ (story: any) => {
+   return { template: '<div id="app"><story /></div>' }
+ }
+]
```
*It is possible that `src/stories/Button.vue` causes an error, in that case the src/stories folder can be deleted*

### Router
Create `src/views/ExampleView/ExampleView.stories.ts`
```ts
import type { Meta, StoryObj } from '@storybook/vue3'
import router from '@/router'

import ExampleView from './ExampleView.vue'
import App from '@/App.vue'

const meta: Meta<typeof ExampleView> = {
  title: 'Examples/View',
  component: ExampleView,
  render: () => ({
    components: { ExampleView },
    template: '<example-view />'
  }),
  tags: ['autodocs']
}

export default meta
type Story = StoryObj<typeof ExampleView>

export const Page: Story = {
  render: () => ({
    components: { ExampleView, App },
    setup() {
      router.replace('/example')
    },
    template: '<app><example-view /></app>'
  }),
  parameters: {
    layout: 'fullscreen'
  }
}

export const Default: Story = {}
```
### Pinia
Update `src/views/ExampleView/ExampleView.stories.ts`
```diff
+ import { useCounterStore } from '@/stores/counter'

+ export const WithInitialCountOf10: Story = {
+ render: () => ({
+   components: { ExampleView },
+   setup() {
+     const counter = useCounterStore()
+     counter.count = 10
+     return {}
+   },
+   template: '<example-view />'
+ })
+}
```

### i18n
Update `.storybook/preview.ts`
```diff
+ import type { StoryContext } from '@storybook/vue3'

+ import i18n from '@/plugins/i18n'

+ export const globalTypes = {
+ locale: {
+   name: 'Locale',
+   description: 'Internationalization',
+   defaultValue: 'en',
+   toolbar: {
+     icon: 'globe',
+     items: i18n.global.availableLocales
+   }
+ }
+}

- (story: any) => {
+ (story: any, context: StoryContext) => {
+   i18n.global.locale = context.globals.locale
```

### Vue Query
*This prevents vue query to retry on fail.*

Update `src/plugins/index.ts`
```diff
- export function registerPlugins(app: App) {
+ export function registerPlugins(app: App, vueQueryRetry = true) {
+ const vueQueryPluginOptions = {
+   queryClientConfig: {
+     defaultOptions: {
+       queries: {
+         retry: vueQueryRetry
+       }
+     }
+   }
+ }

-   .use(VueQueryPlugin)
+   .use(VueQueryPlugin, vueQueryPluginOptions)
}
```

Update `.storybook/preview.ts`
```diff
import { registerPlugins } from '../src/plugins'
+ import { QueryClient } from '@tanstack/vue-query'
+
+ const queryClient = new QueryClient()

-   registerPlugins(app)
+   registerPlugins(app, false)
+   // cancel queries when navigating between stories
+   queryClient.cancelQueries()
```

### MSW
[Storybook msw addon](https://storybook.js.org/addons/msw-storybook-addon)

`yarn add -D msw-storybook-addon`

Update `.storybook/preview.ts`
```diff
+ import { initialize, mswDecorator } from 'msw-storybook-addon'
+ import { allHandlers } from '@mocks/handlers'

+ // Initialize MSW
+ initialize({
+   onUnhandledRequest: 'bypass'
+ })
+ 
  export const decorators = [
+   mswDecorator,

export const parameters = {
+ msw: {
+   handlers: allHandlers
+ }
}
```

Update `/src/views/ExampleView/ExampleView.stories.ts`
```diff
import router from '@/router'
+ import { rest } from 'msw'
+ import examples from '@mocks/api/examples/examples.json'

export const Default: Story = {}

+ export const OneResult: Story = {
+  parameters: {
+    msw: {
+      handlers: [
+        rest.get(
+          `${import.meta.env.VITE_API_HOST}examples`,
+          (req, res, ctx) => {
+            return res(
              ctx.status(200),
+              ctx.json({
+                ...examples,
+                total: 1,
+                data: examples.data.slice(0, 1)
+              })
+            )
+          }
+        )
+      ]
+    }
+  }
+ }
+
+ export const Loading: Story = {
+  parameters: {
+    msw: {
+      handlers: [
+        rest.get(
+          `${import.meta.env.VITE_API_HOST}examples`,
+          (req, res, ctx) => {
+            return res(ctx.status(200), ctx.delay(999999), ctx.json({}))
+          }
+        )
+      ]
+    }
+  }
+ }
+
+ export const Error: Story = {
+  parameters: {
+    msw: {
+      handlers: [
+        rest.get(
+          `${import.meta.env.VITE_API_HOST}examples`,
+          (req, res, ctx) => {
+            return res(ctx.status(404))
+          }
+        )
+      ]
+    }
+  }
+ }
```

Update `src/components/Example/ExampleComponent.stories.ts`
```diff
+ import examples from '@mocks/api/examples/examples.json'

-   example: {
-     id: 7,
-     name: 'sand dollar',
-     year: 2006,
-     color: '#DECDBE',
-     pantone_value: '13-1106'
-   }
+   example: examples.data[0]
```

### e2e tests
[Storybook interaction testing](https://storybook.js.org/docs/react/writing-tests/interaction-testing)
```sh
yarn add -D @storybook/testing-library @storybook/jest @storybook/test-runner
```

Update `.storybook/main.ts`
```diff
stories: [
    '../src/**/*.stories.@(js|jsx|ts|tsx)'
+   '../e2e/**/*.stories.@(js|jsx|ts|tsx)'
  ],
+  features: {
+   interactionsDebugger: true
+ },
```

Create `e2e/example/ExampleFlow.stories.tsx`
```tsx
import type { Meta, StoryObj } from '@storybook/vue3'
import router from '@/router'
import { within, waitFor, userEvent } from '@storybook/testing-library'
import { expect } from '@storybook/jest'

import App from '../../src/App.vue'

const meta: Meta<typeof App> = {
  title: 'Examples/Flow',
  component: App,
  render: () => ({
    components: { App },
    setup() {
      router.replace('/')
    },
    template: '<app />'
  }),
  parameters: {
    layout: 'fullscreen'
  }
}

export default meta
type Story = StoryObj<typeof App>

export const Default: Story = {}

/*
 * See https://storybook.js.org/docs/7.0/react/writing-stories/play-function#working-with-the-canvas
 * to learn more about using the canvasElement to query the DOM
 */
export const Navigate: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement)

    await waitFor(async () => {
      await expect(canvas.getByText('Example'))
    })

    const exampleLink = await canvas.getByText('Example')
    await userEvent.click(exampleLink)
  }
}
```

Update `package.json`
```diff
  "scripts": {
+   "test-storybook": "test-storybook"
  },
```
### Other addons
- [Accessibility](https://storybook.js.org/addons/@storybook/addon-a11y)
- [Designs](https://storybook.js.org/addons/storybook-addon-designs)
- [Chromatic](https://storybook.js.org/addons/chromatic)

## Other libraries
- [Vueuse](https://vueuse.org/)
- [dayjs](https://day.js.org/)
- [echarts](https://github.com/ecomfe/vue-echarts)

## Unused files that can be deleted
- e2e\vue.spec.ts
- public\favicon.ico (replace with own)
- styles (create [SMACSS](http://smacss.com/) structure if necessary)
  - src\assets\styles\base.css
  - src\assets\styles\main.css
- src\assets\logo.svg
- src\components\__tests__ (tests are in the components/views folders)
- src/components/icons
- src/components/HelloWorld.vue
- Home page components after updating the home page
  - src/components/TheWelcome.vue
  - src/components/WelcomeItem.vue
- src/views/AboutView.vue
- src/stories

## Folder structure
- .mocks*
  - api
  - handlers.ts
- .storybook*
  - main.ts
  - preview-head
  - preview.ts
- e2e*
  - *testName*
    - *testName*.spec.ts
    - *testName*.stories.ts
- public
  - favicon.ico
  - mockServiceWorker.js*
- src
  - assets
    - styles
      - _transitions.scss*
      - styles.scss
      - tailwind.css*
    - images
  - components
    - *ComponentName*
      - *ComponentName*.spec.ts*
      - *ComponentName*.stories.ts*
      - *ComponentName*.vue
  - locales*
    - *language*.json
  - models
    - *type*.types.ts
  - plugins
    - index.ts
    - i18n.ts*
    - vuetify.ts*
    - webfontloader.ts*
  - router
    - index.ts
  - stores*
    - *storeName*.ts
  - views
    - *ViewName*View
      - *ViewName*View.stories.ts*
      - *ViewName*View.vue
  - App.vue
  - main.ts
- .env
- .env.example
- .env.msw*
- eslintrc.cjs
- .gitattributes
- .gitignore
- .prettierrc.json
- env.d.ts
- index.html
- package-lock.json
- playwright.config.ts*
- README.md
- tsconfig.app.json
- tsconfig.config.json
- tsconfig.json
- tsconfig.vitest.json*
- yarn.lock