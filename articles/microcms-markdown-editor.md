---
title: 'microCMSの拡張フィールドを用いてMarkdown形式で入力できるようにする（mermaid記法のプレビュー対応）'
emoji: '🧜‍♀'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['microcms', 'markdown', 'mermaid']
published: true
---

# はじめに

こちらの記事は、Markdownで作成した記事をmicroCMSのリッチエディタにコピペしたときに、Markdown記法が適切に認識されない問題への解決策を記述したものです。

詳細につきましては[リポジトリ](https://github.com/kuroweb/microcms-markdown-editor)を参照してください。

# 原因

microCMSのエディタはリッチエディタと呼ばれるもので、Markdown形式で入力したときにリッチテキストに自動変換することで、Markdown記法に対応しているためです。

また、microCMSのリッチエディタの仕様上、「スペース・改行」を入力したタイミングでリッチテキストに自動変換するため、Markdown記法で作成済みのテキストをコピペしても自動変換が上手く動かないわけですね。。。

# 解決策

今回の解決策としては、リッチテキストではなく、Markdown記法そのもので記事を管理できるようにします。

microCMSには拡張フィールドと呼ばれるものがあり、microCMSからiframeで自身のアプリケーションを開いてコンテンツを操作することができます。

これを利用して、Next.jsでMarkdownエディタを実装してVercelにデプロイ、microCMSのiframeでアプリケーションのエディタを表示することで、Markdown記法のプレーンテキストで記事管理をできるようにしていきます。

# 実装

## 基本構成

- Next.js
  - `npx create-next-app@latest --ts`で自動生成したNext.jsアプリケーションをベースに実装していく
- [md-editor-rt](https://github.com/imzbf/md-editor-rt)
  - Markdownエディタを実装できるライブラリ
  - mermaid記法に対応しており、エディタとしての機能が豊富でプレビューが美しい
- [microcms-field-extension-react](https://github.com/microcmsio/microcms-field-extension)
  - microCMSとiframeでデータをやり取りするためのSDK

## Markdownエディタ画面を実装

`pages/index.tsx`

```ts
// common
import { useEffect, useState } from 'react'

// package
import { useFieldExtension } from 'microcms-field-extension-react'
import { MdEditor } from 'md-editor-rt'

// styles
import 'md-editor-rt/lib/style.css'

const Page = () => {
  const [markdown, setMarkdown] = useState<string>('')

  const { data, sendMessage } = useFieldExtension('', {
    origin: process.env.NEXT_PUBLIC_MICROCMS_ORIGIN,
    height: 600,
  })

  useEffect(() => {
    if (data) {
      setMarkdown(data)
    }
  }, [data])

  useEffect(() => {
    sendMessage({ data: markdown })
  }, [markdown, sendMessage])

  return (
    <div data-color-mode="light">
      <MdEditor
        modelValue={markdown}
        onChange={(value) => setMarkdown(value)}
        language="en-US"
        style={{ height: 600 }}
      />
    </div>
  )
}

export default Page
```

`microcms-field-extension-react`で`data`と`sendMessage`を利用しました。

- `data`
  - 記事の入力値が入ってきます。
  - 初期値が`''`で、非同期で少し遅れたタイミングで入力済みの値が反映するような動きをします
- `sendMessage`
  - 入力値をmicroCMSに送信して反映する関数です
  - 今回の実装において、本当なら`onChange`のタイミングで`sendMessage`を呼び出したかったのですが、上手く動いてくれなかったので`useEffect`でエディタの入力値を変更したタイミングで動作させるような実装にしました

`.env`で`NEXT_PUBLIC_MICROCMS_ORIGIN`として自身のmicroCMSのオリジンを定義してください。（でないと`sendMessage`でエラーが発生します。）

## 動作確認

microCMSの拡張フィールドでエディタのURLを入力してあげると、以下のようにmicroCMSからエディタを操作できるようになります。

![](/images/microcms-markdown-editor/image-1.png)

mermaid記法のプレビューもバッチリですね。

![](/images/microcms-markdown-editor/image-2.png)

# まとめ

GitHubのリポジトリでMarkdown記法の記事を管理しつつ、microCMS＋Next.jsで作成した技術ブログを運用しようとした際に、コピペしたときにmicroCMSに上手く反映されず困っていたのですが、本記事の手法でなんとかなりました。

[リポジトリ](https://github.com/kuroweb/microcms-markdown-editor)のうち、`.env`の中身を書き換えるだけで誰でも利用できるので、困っている方のお役に立てると幸いです。
