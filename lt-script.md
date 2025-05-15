# Vue.js Options APIの課題とComposition APIのメリット - 発表原稿

## タイトル・はじめに（20秒）
みなさん、こんにちは！今日は「Vue.js Options APIの課題とComposition APIのメリット」について話します。

実は前職でVue2のOptions APIを使ってたんですが、Vue3が出てComposition APIが推されるようになって、「なんでわざわざ変えたんだろう？」って思ってたんです。なので今回は自分のためにも、OptionsAPI→CompositionAPIで何がよくなったのか整理してみました。

## Options APIとは（30秒）
まずOptions APIですが、Vue2から使われている従来の書き方です。

```javascript
export default {
  data() { return { /* データ */ } },
  methods: { /* メソッド */ },
  computed: { /* 算出プロパティ */ }
}
```

みたいに、data, methods, computedといったオプションごとにコードを書いていく方法ですね。多くの方が使ったことあると思います。

## Composition APIとは（30秒）
で、Vue3で導入されたのがComposition APIです。

```javascript
export default {
  setup() {
    const count = ref(0)
    const doubled = computed(() => count.value * 2)
    
    function increment() {
      count.value++
    }
    
    return { count, doubled, increment }
  }
}
```

setup関数の中にすべてのロジックを書いて、テンプレートで使うものをreturnするという書き方になります。refやreactiveを使うのが特徴的ですね。

## 課題1: コードが散在する（60秒）
で、Options APIの課題の1つ目が「コードの散在」です。

例えば、あるデータとそれに関連するメソッド、算出プロパティがあったとして、Options APIだとそれぞれdata, methods, computedと別々の場所に書かないといけないんですよね。

（画像を指して）この図を見てもらうとわかりやすいと思うんですが、同じ機能に関するコードなのにバラバラの場所に書くことになります。

対してComposition APIだと、関連するロジックを一箇所にまとめられるようになります。例えば、カウンターの機能なら、カウントの値、増減のメソッド、計算された値を全部セットで近くに書けるんです。

こうすると、あるロジックを理解したり修正したりするときに、あちこち行ったり来たりしなくて済むようになります。

## 課題2: 機能の再利用性（60秒）
2つ目の課題が「機能の再利用性」です。

Vue2でも、もちろんコードを再利用する方法はありました。ユーティリティ関数として切り出したり、ミックスインを使ったりね。でもこれらには問題がありました。

ユーティリティ関数だと状態（data）の共有ができないし、リアクティブな連携も複雑になりがちでした。

ミックスインはもっと複雑で、名前の衝突とか、どのミックスインが何を提供しているのかがわかりにくくなるとか、mixinAとmixinBがあったらどっちが優先されるのかとか...使っていくうちにカオスになりがち。

Composition APIではコンポーザブル関数という形で機能を切り出せます。
```javascript
export function useValidation(initialValue = '') {
  const value = ref(initialValue);
  const error = ref('');
  
  const validateEmail = () => {
    error.value = !value.value.includes('@') 
      ? 'メールアドレスが無効です' : '';
  };
  
  return { value, error, validateEmail };
}
```

これなら状態もロジックもセットで使えるし、名前の衝突も分割代入で名前を変えれば回避できます。すごく明示的でわかりやすい！

## 課題3: TypeScriptとの相性（60秒）
3つ目はTypeScriptとの相性です。

Options APIでTypeScriptを使おうとすると、かなり大変なんです。このように複雑な型定義が必要になったり...

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

Vue.extendを使えば型定義は可能ではあるんですが、正直めんどくさい。特に大きなコンポーネントだとコストが高すぎる。

対してComposition APIならTypeScriptとの相性がすごくいいんです。標準的なTypeScript機能がそのまま使えるし、型推論も効くようになります。

## まとめ（40秒）
というわけで、OptionsAPI→CompositionAPIで以下のことが解決されました！

* 関連ロジックをまとめられるようになって保守性が向上
* コンポーザブル関数で状態とロジックが再利用しやすくなった
* TypeScriptがすごく使いやすくなった

僕自身、今回LTの準備をしてみて「なるほど、だからComposition APIに移行したのか」とすごく納得できました。もし皆さんがまだOptions APIを使っているなら、ぜひComposition APIを試してみてください。

以上です！ありがとうございました！ 