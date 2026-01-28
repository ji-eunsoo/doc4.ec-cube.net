---
title: テンプレートのカスタマイズ
keywords: core カスタマイズ テンプレート
tags: [core, template]
permalink: customize_template
folder: customize

---

## 概要

EC-CUBE4系では、セキュリティおよび責務分離の観点から、
管理画面で作成した「ブロック」や、`Resource/template/default/Block`配下のブロックテンプレートを、テンプレート内の任意の場所で呼び出すための専用 Twig 関数が提供されています。

この仕組みを利用することで、管理画面で作成したブロックや、  
`Resource/template/default/Block` 配下に配置したブロックテンプレートを、  
テンプレート内の任意の場所で呼び出すことができます。

本ページでは、Twig 関数を利用したブロック呼び出し方法について説明します。

## Twig関数によるブロック呼び出し [#2263](https://github.com/EC-CUBE/ec-cube/pull/2263){:target="_blank"}

EC-CUBEが提供するTwig拡張により、`eccube_block_<block_name>()` という形式の関数を用いて、特定のブロックを直接レンダリングできます。

呼び出し対象として、いずれかに該当するブロックが対象となります。
- 管理画面の  
  [コンテンツ管理] > [ブロック管理] で作成されたブロック
- `Resource/template/default/Block` 配下に Twig ファイルを配置し、ブロック管理で登録されたブロック

※`Block` ディレクトリに Twigファイルを配置しただけではブロックとしては認識されません。
　必ず管理画面の「ブロック管理」でブロックを作成・有効化する必要があります。

## 基本的な使い方
テンプレートファイル（.twig）内で以下のように記述します。

```twig
{% raw %}
{# 例: ロゴブロック（logo.twig）を呼び出す場合 #}
{{ eccube_block_logo() }}

{# 例: カテゴリブロック（category.twig）を呼び出す場合 #}
{{ eccube_block_category() }}
{% endraw %}
```
通常、ブロックの配置は管理画面の 「レイアウト管理」 により行われます。
この Twig 関数を使用することで、レイアウト枠（サイドバー・フッターなど）に依存せず、テンプレート内の任意の位置にブロックを埋め込むことが可能です。


## 活用例
特定ページへの固定表示を行いたい場合など、ページテンプレートの特定の HTML 構造の中にブロックを直接組み込むことができます。
また、Twig の条件分岐と組み合わせることで、条件付きで表示されるブロックを実装できます。

```twig
{% raw %}
{% if app.user %}
  {{ eccube_block_member_banner() }}
{% endif %}
{% endraw %}
```
