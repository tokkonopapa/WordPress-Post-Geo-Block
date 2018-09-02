---
layout: page
language: ja
category: codex
section: Dashboard
title: 検証ルールの設定
---

このプラグインは、IPアドレスの位置情報を元にした遮断以外にも、幾つかの追加的ルールに基づいた
検証を行うことで、悪意のあるリクエストを遮断します。このセクションでは、プラグイン全般に渡る
検証のルールと遮断時の振る舞いを設定します。

<!--more-->

#### あなたのIPアドレス / 国コード ####

ここに示されたIPアドレスが、本プラグインが認識しているあなたのIPアドレスと国コードです。
「国コードを検索する」ボタンは、あなたのIPアドレスの国コードを複数の位置情報データベースから
検索して表示します。稀に、データベースによって異なる国コードを示すことがあります。
誤った国コードが示された場合には、「**位置情報APIの設定**」で、該当するデータベースのチェック
を外すことをお勧めします。

また国コードが「XX (Private)」と表示される場合、あなたのサーバーは、リバース・プロキシーか
ロードバランサーの背後など、プライベートなネットワーク環境に設置されている事を示しています。
パブリックなIPアドレスが取得できる様、後述する「**IPアドレスを追加抽出する$_SERVERのキー**」
に [`HTTP_X_FOWARDED_FOR`][X-Forwarded] など、PHPで取得可能なHTTPヘッダフィールドの
キーを設定して下さい。

#### マッチング規則 ####

「ホワイトリスト」または「ブラックリスト」のどちらか一方を選択します。この選択に応じて、次項の
タイトルとテキスト・ボックスが変化します。

#### 国コードの[ホワイトリスト|ブラックリスト] ####

前項「**マッチング規則**」の選択に従い、[ISO 3166-1 alpha-2][ISO-3166-1] に定義された
アルファベット2文字の国コードを指定します。

#### AS番号を使用可能にする ####

[AS番号][AS-Number] は、IPアドレス群のグループに割り当てられた番号です。例えば Facebook 
は多くのIPアドレスを所有していますが、[AS32934][AS32934] が割り当てられています。
この設定を有効化することで、特定組織のIPアドレス群をまとめて指定することができる様になります。

#### 国コードに優先して検証するIPアドレスの[ホワイトリスト|ブラックリスト] ####

国コードの検証に先立ち、遮断または通過させるIPアドレス群やAS番号を指定します。連続する
IPアドレスは [CIDR][CIDR] というシンプルな表記が可能なので、
「**IPv4/IPv6用CIDR変換電卓**」を活用して所望のアドレス群を設定して下さい。

![IPv4/IPv6用CIDR変換電卓]({{ '/img/2018-03/CIDR-Calculator.png' | prepend: site.baseurl }}
 "IPv4/IPv6用CIDR変換電卓"
)

#### IPアドレスを追加抽出する $_SERVER のキー ####

プロキシーサーバーを経由したリクエストの場合、経由した複数サーバーのIPアドレスが特定のHTTP
ヘッダフィールドに格納される場合があります。その様なIPアドレスの全てを検証の対象とするために、
`HTTP_X_FORWARDED_FOR`や`HTTP_CLIENT_IP`など、PHPで取得可能なキーを設定します。

#### 悪意のあるシグネチャ ####

悪意のあるリクエストを遮断するために、[クエリ][Query]から検索すべき悪意のある文字列（コメント
や記事の投稿は除きます）を指定します。

#### 悪意のあるアップロード防止 ####

プラグインやテーマの脆弱性を狙った、悪意のあるファイルのアップロードを阻止するためのルールを
設定します。

- **拡張子とMIMEタイプを検証**  
  許可するMIMEタイプのホワイトリスをと設定します。

- **ファイル拡張子のみを検証**  
  禁止するファイル拡張子のブラックリストを設定します。

- **検証する権限**  
  アップロードに必要な権限を設定します。詳しくは [Roles and Capabilities][Roles] を参照
  して下さい。

#### IPアドレス当たりのログイン試行可能回数 ####

ログイン試行可能な最大回数を設定します。後述する「**プライバシーと記録の設定**」の
「**IPアドレスをキャッシュに記録**」を有効化する必要があります。

#### レスポンス・コード ####

遮断時に応答する [HTTPステータスコード][HTTP-Status] を指定します。選択に応じて以下の2つを
設定します。

- **リダイレクト先 URL**  
  2XX および 3XX の場合は、リダイレクト先の URL（デフォルトは 
  [blackhole.webpagetest.org][Blackhole]）を指定します。

- **レスポンス・メッセージ**  
  4XX および 5XX は、シンプルなインターフェース [`wp_die()`][WP-DIE] で表示するメッセージ
  を指定します。別途、テーマの `404.php` をテンプレートとする 
  [ヒューマン・フレンドリーなエラーページ][Error-Page] を設定することが出来ます。

#### 検証のタイミング ####

このプラグインによる検証が実行されるタイミングを指定します。

通常、プラグインを安全に実行できるタイミングは [アクション・フック][Action-Hook] の `init`
ですが、テーマやプラグインの読み込み後となるため、遮断時には無駄なサーバー資源を消費することに
なります。この無駄を省くため、`muplugins_loaded` を選択することが出来ます。

### 参考情報 ###

- [The best practice of target settings][BestPractice]
- [Prevent exposure of wp-config.php][ExposeWPConf]

[IP-Geo-Block]: https://wordpress.org/plugins/ip-geo-block/ "WordPress › IP Geo Block « WordPress Plugins"
[BestPractice]: {{ '/codex/the-best-practice-for-target-settings.html' | prepend: site.baseurl }} "The best practice of target settings | IP Geo Block"
[ExposeWPConf]: {{ '/article/exposure-of-wp-config-php.html'           | prepend: site.baseurl }} "Prevent exposure of wp-config.php | IP Geo Block"
[ISO-3166-1]:   https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements "Officially assigned code elements - Wikipedia"
[AS-Number]:    https://en.wikipedia.org/wiki/Autonomous_system_(Internet) "Autonomous system (Internet) - Wikipedia"
[AS32934]:      https://ipinfo.io/AS32934 "AS32934 Facebook, Inc. - ipinfo.io"
[CIDR]:         https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing "Classless Inter-Domain Routing - Wikipedia"
[Query]:        https://en.wikipedia.org/wiki/Query "Query - Wikipedia"
[X-Forwarded]:  https://en.wikipedia.org/wiki/X-Forwarded-For "X-Forwarded-For - Wikipedia"
[Roles]:        https://codex.wordpress.org/Roles_and_Capabilities "Roles and Capabilities &laquo; WordPress Codex"
[HTTP-Status]:  https://en.wikipedia.org/wiki/List_of_HTTP_status_codes "List of HTTP status codes - Wikipedia"
[WP-DIE]:       https://codex.wordpress.org/Function_Reference/wp_die "Function Reference/wp die &laquo; WordPress Codex"
[Error-Page]:   {{ '/codex/customizing-the-response.html#human-friendly-error-page' | prepend: site.baseurl }} "Response code and message | IP Geo Block"
[Action-Hook]:  https://codex.wordpress.org/Plugin_API/Action_Reference "Plugin API/Action Reference &laquo; WordPress Codex"
[Blackhole]:    http://blog.patrickmeenan.com/2011/10/testing-for-frontend-spof.html "Performance Matters: Testing for Frontend SPOF"