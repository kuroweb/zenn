---
title: 'microCMSの拡張フィールドを用いてMarkdown形式で入力できるようにする（mermaid記法のプレビュー対応）'
emoji: '🧜‍♀'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: ['microcms', 'markdown', 'mermaid']
published: false
---

# はじめに

こちらの記事は、Markdownで作成した記事をmicroCMSのリッチエディタにコピペしたときに、Markdown記法が適切に認識されない問題への解決策を記述したものです。

# 原因

microCMSのエディタはリッチエディタと呼ばれるもので、Markdown形式で入力したときにリッチテキストに自動変換することで、Markdown記法に対応しているためです。

また、microCMSのリッチエディタの仕様上、「スペース・改行」を入力したタイミングでリッチテキストに自動変換するため、Markdown記法で作成済みのテキストをコピペしても自動変換が上手く動かないわけですね。。。

# 解決策

今回の解決策としては、リッチテキストではなく、Markdown記法そのもので記事を管理できるようにします。

microCMSには拡張フィールドと呼ばれるものがあり、microCMSからiframeで自身のアプリケーションを開いてコンテンツを操作することができます。

これを利用して、Next.jsでMarkdownエディタを実装してVercelにデプロイ、microCMSのiframeでアプリケーションのエディタを表示することで、Markdown記法のプレーンテキストで記事管理をできるようにしていきます。

# 実装

# まとめ
