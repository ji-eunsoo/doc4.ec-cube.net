---
layout: single
title: Controllerのカスタマイズ
keywords: core カスタマイズ コントローラ
tags: [core, controller]
permalink: customize_controller
folder: customize
---


---

## 新しいルーティングの追加

`#[Route]` 属性（Attribute）を付与したコントローラクラスを`./app/Customize/Controller/` 以下に配置することで、サイトに新しいルーティングを追加することが可能です。  

以下は最もシンプルなルーティング追加の例です。  
`http://サイトURL/sample` にアクセスすると"Hello sample page !"と表示するルーティングを追加しています。

### Controllerファイル

./app/Customize/Controller/SamplePageController.php

```php
<?php

namespace Customize\Controller;

use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class SamplePageController
{
    #[Route('/sample', methods: ['GET'])]
    public function testMethod(): Response
    {
        return new Response('Hello sample page !');
    }
}
```

## テンプレートファイルの利用

`#[Template]` 属性（Attribute）を利用することで、Twigのテンプレートファイルを利用することができます。
以下のサンプルは、`http://サイトURL/sample` にアクセスすると"Hello EC-CUBE !"と表示します。

### Controllerファイル

```php
<?php

namespace Customize\Controller;

use Eccube\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;

class SamplePageController extends AbstractController
{
    #[Route('/sample', methods: ['GET'])]
    public function testMethod(): Response
    {
        return $this->render('Sample/index.twig', [
            'name' => 'EC-CUBE',
        ]);
    }
}
```


### Twigファイル

./app/template/default/Sample/index.twig

```twig
{% raw %}<h3>Hello {{ name }}</h3>{% endraw %}
```

## カスタマイズのヒント

### URLからパラメータを受け取る

`http://サイトURL/sample/1` のようにURLに含まれるパラメータを変数の値として受け取ることができます。  
`#[Route]` 属性（Attribute）で定義したパスの `{id}` の部分は、コントローラーメソッドの引数に同名の変数 `$id` を定義することで、自動的に受け取れます。

```php
    #[Route('/sample/{id}', methods: ['GET'])]
    public function testMethod($id): Response
    {
        return new Response('Parameter is '.$id);
    }
```

### 追加したルーティングへのリンクをする

他のページのテンプレートファイルから、追加したルーティングにリンクをするには、ルーティングに名前をつける必要があります。  
`#[Route]` 属性（Attribute）の name パラメータを指定することで、ルーティング名を設定できます。

```php
    #[Route('/sample/{id}', name: 'sample_page', methods: ['GET'])]
    public function testMethod($id): Response
    {
        return new Response('Parameter is '.$id);
    }
```

 他のページのテンプレートファイルからリンクをする場合には、以下のように記述します。  
 パラメータを渡すことも出来ます。

```twig
{% raw %}
<a href="{{ path('sample_page', { id: 1 }) }}">サンプルページへ</a>
{% endraw %}
```

### EC-CUBE既存のルーティングを上書きする

EC-CUBE既存のルーティングを上書きするには、同じパスと名前でルーティングを定義します。  
下記のサンプルでは、「当サイトについて」のページを上書きしています。

```php
    #[Route('/help/about', name: 'help_about', methods: ['GET'])]
    public function testMethod(): Response
    {
        return new Response('Overwrite /help/about page');
    }
```

### 管理画面のルーティングを追加する

管理画面にログインしているユーザーのみがアクセスできるルーティングを追加する場合には、パスに `/%eccube_admin_route%` を利用します。

```php
    #[Route('/%eccube_admin_route%/sample', methods: ['GET'])]
    public function testMethod(): Response
    {
        return new Response('admin page');
    }
```

同様にUserDataへのルーティングは `/%eccube_user_data_route%` を指定します。

### リダイレクトを行う

AbstractControllerを継承して `redirectToRoute` 関数を利用することでリダイレクトが可能です。  
下記のサンプルでは、アクセスがあると「当サイトについて」のページへリダイレクトしています。

```php
    #[Route('/sample', methods: ['GET'])]
    public function testMethod(): RedirectResponse
    {
        return $this->redirectToRoute('help_about');
    }
```

また `forward()` メソッドを利用することで、リダイレクトではなく内部的に他のコントローラーへ処理を渡すことができます。

### Controller内でサービスを利用する

`AbstractController` あらかじめコントローラに注入されているサービスをプロパティとして利用できます。
以下のサンプルでは、EntityManagerを利用して商品のEntityを取得しています。

```php
<?php

namespace Customize\Controller;

use Eccube\Controller\AbstractController;
use Eccube\Entity\Product;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class SamplePageController extends AbstractController
{
    #[Route('/sample', methods: ['GET'])]
    public function testMethod(): Response
    {
        /** @var Product $product */
        $product = $this->entityManager->getRepository(Product::class)->find(1);

        return new Response('Product is '.$product->getName());
    }
}
```

EntityManger以外に、AbstractControllerを継承することで利用できるサービスは `./src/Eccube/Controller/AbstractController.php` を確認してください。  

#### AbstractControllerに無いサービスを利用する

インジェクションを利用することで、AbstractController であらかじめ用意されていないサービスのインスタンスも自由に利用できます。
以下のサンプルでは、BaseInfoからショップ名を取得しています。

```php
<?php

namespace Customize\Controller;

use Eccube\Repository\BaseInfoRepository;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;

class SamplePageController
{
    /** @var \Eccube\Entity\BaseInfo */
    protected $BaseInfo;

    public function __construct(BaseInfoRepository $baseInfoRepository)
    {
        $this->BaseInfo = $baseInfoRepository->get();
    }

    #[Route('/sample', methods: ['GET'])]
    public function testMethod(): Response
    {
        return new Response('Shop name is '.$this->BaseInfo->getShopName());
    }
}
```

### 画面を表示する必要がないコントローラーを作成する

画面を表示する必要のないコントローラの場合も必ずResponseオブジェクトを返してください。  
(`exit()` で処理を終了するとEC-CUBEが正常な動作を行えなくなります)  

Responseのレスポンスコードやヘッダーを指定することも可能です。

```php
    #[Route('/sample', methods: ['GET'])]
    public function testMethod(): Response
    {
        return new Response(
            '',
            Response::HTTP_OK,
            ['Content-Type' => 'text/plain; charset=utf-8']
        );
    }
```

```
$ curl -D - http://サイトURL/sample
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8
```

## 参考情報

EC-CUBE 4 ではSymfonyのController機構を利用しています。  
その他のカスタマイズ方法についてはSymfonyのドキュメントを参照してください。

[Controller](https://symfony.com/doc/current/controller.html){:target="_blank"}