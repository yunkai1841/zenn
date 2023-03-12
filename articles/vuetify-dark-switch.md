---
title: "Vuetify3でダークテーマに切り替える"
emoji: "🌙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vuetify", "vue", "javascript"]
published: true
---

Vue3でsetup scriptを使っている場合に、Vuetifyのグローバルテーマを切り替える方法を紹介します。

# Vuetify3のテーマにアクセスする
Vuetify3では、グローバル設定にアクセスするために、`useTheme`を使います。
`theme.global.name.value`にアクセスすることで、グローバルテーマの名前を取得できます。
この値を`light`か`dark`に変更することで、グローバルテーマを切り替えることができます。

```js
import { useTheme } from 'vuetify'

const theme = useTheme()
console.log(theme.global.name.value)
```

また、テーマの設定は`createVuetify`で行います。
`light`や`dark`以外にも設定可能で、自分で色を定義することもできます。
Vuetify3公式ドキュメントを参考にしてインストールした場合、`src\plugins\vuetify.js`に設定が書かれています。


# 実装例
ダークモード
![picture 1](/images/vuetify-dark-switch/vuetify-theme-dark.png)

ライトモード
![picture 2](/images/vuetify-dark-switch/vuetify-theme-light.png)  

ソースコード
```vue
<template>
  <v-app-bar flat>
    <v-app-bar-title>
      <v-icon icon="mdi-calculator-variant" />

      Title
    </v-app-bar-title>

    <!-- dark theme switch -->
    <template v-slot:append>
      <v-switch
        v-model="darkTheme"
        @update:model-value="changeTheme"
        :prepend-icon="darkTheme ? 'mdi-weather-night' : 'mdi-weather-sunny'"
        hide-details
        inset
        class="mr-auto"
      />
    </template>
  </v-app-bar>
</template>

<script setup>
import { ref } from 'vue'
import { useTheme } from 'vuetify'

const darkTheme = ref(false)
const theme = useTheme()

const changeTheme = () => {
  theme.global.name.value = darkTheme.value ? 'dark' : 'light'
}
</script>
```

# 参考
https://vuetifyjs.com/en/features/theme/
https://vuetifyjs.com/en/api/use-theme/