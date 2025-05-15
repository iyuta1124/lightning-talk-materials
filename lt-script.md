# Vue.js Options APIの課題とComposition APIのメリット - 発表原稿

## 1. はじめに（20秒）
今日は「Vue.js Options APIの課題とComposition APIのメリット」についてお話しします。

前職でVue2のOptions APIを使っていたのですが、Vue3でComposition APIが推されるようになって「なぜ変わったのか？」と疑問に思っていました。今回はその理由を整理して、皆さんと共有したいと思います。

## 2. Options APIとは（30秒）
まずOptions APIですが、Vue2から使われている従来の書き方です。

```javascript
export default {
  data() { return { count: 0 } },
  methods: {
    increment() { this.count++ }
  },
  computed: {
    doubled() { return this.count * 2 }
  }
}
```

このように、data, methods, computedなどのオプションごとにコードを分けて書くスタイルです。

## 3. Composition APIとは（30秒）
Vue3で導入されたのがComposition APIです。

```javascript
import { ref, computed } from 'vue'

const count = ref(0)
const doubled = computed(() => count.value * 2)
function increment() { count.value++ }
```

setup関数の中にロジックをまとめて書き、テンプレートで使うものをreturnします。

## 5. Options APIの課題（60秒×3）

### 課題1: コードが散在する
Options APIでは、同じ機能に関するデータ・メソッド・算出プロパティが別々の場所に書かれます。これだと、ロジックを理解・修正する時にあちこち探す必要があり、保守性が下がります。

### 課題2: 機能の再利用が難しい
Options APIでもユーティリティ関数やミックスインで再利用はできますが、
- ユーティリティ関数は状態（data）の共有ができない
- ミックスインは名前の衝突や、どのミックスインが何を提供しているか分かりづらい

特にミックスインは複雑化しやすく、保守が大変です。

### 課題3: TypeScriptとの相性が悪い
Options APIでTypeScriptを使う場合、型定義が煩雑になりがちです。

```typescript
interface ComponentData { user: { name: string; email: string; } }
interface ComponentMethods { updateUser(name: string): void; }

export default Vue.extend({
  data(): ComponentData {
    return { user: { name: '', email: '' } }
  },
  methods: {
    updateUser(newName: string) {
      this.user.name = newName;
    }
  }
});
```

Vue.extendを使えば型定義はできますが、特に大きなコンポーネントだとコストが高いです。

## 5. Composition APIでどう解決できるか（60秒×3）

### 解決1: コードをまとめて書ける
Composition APIなら、関連するロジックをsetup関数内にまとめて書けます。カウンター機能なら、値・メソッド・算出値をセットで管理でき、理解や修正が楽になります。

### 解決2: 機能の再利用が簡単
コンポーザブル関数として状態とロジックをセットで切り出せます。

```vue
<script setup>
import { useValidation } from './useValidation'
const { value, error, validateEmail } = useValidation()
</script>
```

### 解決3: TypeScriptとの相性が良い
Composition APIは標準的なTypeScript機能がそのまま使え、型推論も効きます。

```vue
<script setup lang="ts">
import { ref, Ref } from 'vue'
const count: Ref<number> = ref(0)
</script>
```

## 6. まとめ（40秒）
Options APIからComposition APIに移行することで、

* 関連ロジックを直感的にまとめやすくなった
* 状態とロジックの再利用がよりシンプルに
* TypeScriptがより自然に使える

今回LTの準備を通して「だからComposition APIに移行したのか」と納得できました。
もしまだOptions APIを使っている方がいれば、ぜひ一度Composition APIを試してみてください。

以上です！ありがとうございました！ 