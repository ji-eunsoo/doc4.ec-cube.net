---
title: ディレクトリ・ファイル構成
keywords: spec directory structure
tags: [spec, getting_started]
permalink: spec_directory-structure

layout: single
---

## 特徴

1. EC-CUBE4 はSymfonyのディレクトリ構造を参考にしつつ、EC-CUBE独自の構成になっています。バージョンごとに、以下のSymfonyの構成を参考にしています。

- 4.0系以降
[Symfony3系のディレクトリ構造](https://symfony.com/doc/3.4/quick_tour/the_architecture.html){:target="_blank"}を参考にしています。
- 4.1系以降
[Symfony4系のディレクトリ構造](https://symfony.com/doc/4.x/quick_tour/the_architecture.html){:target="_blank"}を参考にしています。
- 4.2系以降
[Symfony5系のディレクトリ構造](https://symfony.com/doc/5.x/quick_tour/the_architecture.html){:target="_blank"}を参考にしています。
- 4.3系以降
[Symfony6系のディレクトリ構造](https://symfony.com/doc/6.4/quick_tour/the_architecture.html){:target="_blank"}を参考にしています。

## 主なディレクトリと役割

### codeception/
- Codeception による自動テスト（主に E2E / Unit）に関する設定・テストコードを配置

```
codeception/
├── acceptance/           E2E（ブラウザ操作）テスト
└── unit/                 単体テスト
```

### app/

- 設定ファイルやプラグイン、EC-CUBEをカスタマイズするPHPコードなど、アプリケーションごとに変更されるファイルを配置

```
app
├── Customize   カスタマイズ用PHPコードを配置
├── Plugin      インストールしたプラグインを配置
├── PluginData  プラグインが利用するファイルを配置
├── config      設定ファイルを配置
├── proxy       Entity拡張機能によって生成されたProxyクラスを配置
└── template    上書きされたテンプレートファイルを配置
```

### bin/

- `bin/console`など、開発に使用する実行ファイルを配置

### html/

- リソースファイル(jsやcssや画像ファイル）を配置

### src/

- EC-CUBE本体となり、phpファイルやTwigファイルを配置

### tests/

- テストコードを配置

### var/

- キャッシュやログファイルなど、実行時に生成されるファイルを配置

### vendor/

- サードパーティの依存ライブラリを配置
