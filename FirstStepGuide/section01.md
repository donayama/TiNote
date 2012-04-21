<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/cover.png" height="240" />

第一章 スマートフォンアプリ開発の第二の選択肢"Titanium Mobile"とは？
==========================================================
Titanium Mobileとは米Appcelerator Inc.によって開発されているモバイルデバイス向けのアプリケーション開発ツールです。
特徴として「JavaScriptによるプログラミングのみでiOSデバイス(iPhone, iPod touch, iPad)やAndroid端末向けのアプリケーションが開発できる」ことが挙げられます。 
本章ではそのおおまかな概念や動作原理、動作させるのに必要となる環境についてについて触れます。

##スマートフォンアプリ開発の第二の選択肢

このドキュメントに興味を持たれたという事は、少なからずスマートフォンアプリケーション開発にご興味・ご関心があるかと思います。

ビジネスで**もともとそういう分野の人間ではない**のにスマートフォン向けアプリケーションを作らないと行けない状態におかれたり、パソコン以上に身近な存在であるスマートフォンで自分のニッチな需要を満たすアプリケーションを作りたいという趣味レベルでいざ「今日から始める〜開発」的な入門書やWebのドキュメントを開くと、Objective-CやJavaといった言語の存在がまず大きな壁となることも少なからずあるでしょう。

あるいはObjective-CやJavaでのプログラム開発が出来る方にとっても、「じゃあこれ、iPhoneだけじゃなくて、Androidにも対応してね！ でも予算はあんまり取れないけど」とか気軽に言われて頭を抱えることは少なくないのではないでしょうか。

そういうプラットフォーム提供者による「第一かつ唯一の選択肢」ではなく、別の手法「第二の複数ある選択肢」のひとつとして、Titanium Mobileがあります。

「複数ある」とわざわざ明記したようにAdobeに買収されたPhoneGap (http://phonegap.com) 、C#などの.NET言語で開発ができるMono touch (http://xamarin.com/monotouch) 、物理演算を使うような2Dゲーム開発に有利なCorona (http://www.anscamobile.com/corona/) などの選択肢がありますが、本書ではネイティブUIを使った違和感の無いUX(ユーザ体験)を提供しやすいTitanium Mobileについて触れていきたいと思います。

##Titanium Mobileとは何か？

Titanium Mobileは Objective-CやJavaといったプラットフォームSDKが想定しているプログラム言語の代わりにJavaScriptでのプログラム開発が行えるツールキットです。

http://www.appcelerator.com/

とはいえ、JavaScriptで記述されたソースプログラムが逐一Objective-CやJavaのソースに翻訳されるわけではありません。あくまでもプログラミング言語としてはJavaScript (ECMAScript)によりコーディングされた内容が実行ファイル内に格納され、デバイス上での実行時にインタプリタ的に動的に解釈されます。
言語ベースでロジックを置き換えるだけではなく、プラットフォームSDKが提供するAPIをより簡単にかつ共通的に操作できるように工夫されています。これらはTitanium Mobile APIとして提供されており、日々進化・洗練されています。

次に挙げる代表的機能はすべてAPIで実現できます。


<table border="2">
<tr>
    <th>Native UI</th>
    <td>ラベル・テキスト入力・ボタン・スライダをはじめとした豊富なUI部品
Webブラウザ機能、地図表示など</td>
</tr>
<tr>
    <th>メディア機能</th>
    <td>画像・動画の表示、音声再生・録音、静止画・ビデオ撮影</td>
</tr>
<tr>
    <th>データI/O</th>
    <td>ファイルシステムの操作、デバイス内データベース(主にアドレス帳)の操作
SQLiteデータベース</td>
</tr>
<tr>
    <th>ネットワーク通信</th>
    <td>XMLHttpRequestだけではなくSocket通信やBonjourにも対応</td>
</tr>
<tr>
    <th>ハードウェアデバイス</th>
    <td>カメラ・GPS・電子コンパス・加速度センサ</td>
</tr>
<tr>
    <th>OS特化の機能</th>
    <td>プッシュ通知機能、バックグラウンドサービス、インテント</td>
</tr>
</table>

プラットフォームOS・デバイスに依存したUIやAPIを利用しない限り、基本的にサポートされるプラットフォーム全てに対してコードを転用できると謳われていますので、JavaScriptによる開発効率の向上だけではなくマルチプラットフォーム展開を少ないコストで実現できることも魅力のひとつとなっています。

また最終的にネイテイブアプリケーションとして動作するため、App StoreやGoogle Play(Android Marketplace)でのアプリ公開も可能です。
Titanium Mobileで開発されていると公言されているアプリケーションは多くないのですが、少なからずアプリケーションが公開されているようです。開発元のAppcelerator社のサイト内で代表的なユーザとしてはNBC Universal、eBay/PayPal/Anheuser-Busch、MTV、Computer Service Corporationが挙げられています。

日本で代表的なアプリケーションとしてはココログ、Zaim、MogSnapなどが挙げられます。

###Titanium Mobileを支えるテクノロジ
Titanium MobileはJavaScriptでアプリケーションの画面操作・ロジックといったところをすべて記述します。

ではそれは実際にはデバイス上でどのように動作するのでしょうか。

ビルド時にバイナリ化されたJavaScriptをインタプリタ的に解釈し、中間層でObjective-CやJavaで構築されたプラットフォームAPIライブラリを呼び出す動作をしています。

	プラットフォームAPIライブラリ
		↑
	Titanium Mobile APIライブラリ
		↑
	JavaScriptエンジン ←→ Titanium Mobileランタイム ← ユーザプログラム(JavaScript)

ネイテイブコンパイルされたソースを動作させるのに比較すると、プログラム部分を動的な解釈をするためどうしても分が悪いのですが、そこから先はネイティブコードと同等の速度となり、iOSデバイスについては部分的にはネイティブコードに近いパフォーマンスを見せています。

Titanium Mobile 1.8でサポートされたGoogle V8 RuntimeによりAndroid環境においてもパフォーマンス面で遜色ないものになりつつあります。

###開発元Appcelerator社について
Appcelerator Inc.（アプセラレータ、以下Appcelerator）はカリフォルニア州マウンテンビュー、いわゆるシリコンバレーに所在するITベンチャー企業です。

CEOのJeff Haynie氏を中心に2011年12月時点で100名弱のスタッフを抱え、Titanium Mobile（タイタニウム モバイル もしくは タイタニアム モバイル）を中心にした同社の各製品の開発、ユーザサポートを行っています。

同社ではかつてはマルチプラットフォーム志向を掲げ、デスクトップ向けにはTitanium Desktop、モバイル向けにはTitanium  Mobileという製品として公開しておりましたが、2012年1月にTitanium Desktopがオープンソースプロジェクトとして切り出される形となり。モバイルに注力する方針を打ち出しました。

2010年3月にTitanium Mobileの正式リリースを行った後、地域情報を活用するTitanium + GeoやPaypalと提携してマイクロペイメントを実現するTitanium + Commerceなどを発表すると共に、eBayからの900万ドルの資金調達 、Web開発環境で著名であったAptana Studioを開発するAptana, Inc.を2011年1月に買収と成長を続けています。

同社のCEOであるJeff Haynie氏自身がGitHubにコミットをし続けていたり、また製品が開発者をターゲットとしたものばかりであるため大変geek色の強い、まさに「開発者による開発者のための企業」です。

そのため、ドキュメントなどのソース以外のものへのアプローチが若干後回しになってきましたが、資金調達で人員の増強などを行った結果、ドキュメント面がようやく充実してきた…と言えるでしょう。

###オープンソース開発企業
Titanium Mobileの特徴のひとつとして、オープンソース開発されている点が挙げられます。

githubのAppceleratorアカウントには各種製品やサンプルのレポジトリが存在しており、Titanium Mobileのソース・ドキュメント・サンプルプログラムがすべてApache License, Version 2.0で公開されています。

この種の中間処理を委ねるツールキットはブラックボックスになりがちなため、何らかのトラブルに直面した時に対処しづらい傾向にありますが、同社の製品については公開されたソースコードを追うことにより製品バグか否かの切り分けもできるというメリットがあります。

###日本で注目を集めるTitanium Mobile
日本では2010年秋頃までは細々としか使われていなかったTitanium Mobileですが、10月以降アルファブロガーと呼ばれる方々がTitanium Mobileに関するプレゼンや記事を発表されたことにより急激に注目をあつめるようになりました。

また、増井雄一郎氏(@masuidrive)が12月よりAppceleratorに参画され、日本でのエバンジェリスト活動をされたことにより、一層認知度が向上し、2012年に日本法人の設立がされる予定となっています。
また、手前味噌ながら筆者が運営する日本語情報発信プロジェクトtitanium-mobile-doc-jaをはじめ日本語での得られる情報が多く存在しており、PhoneGapやColona、Mono touchといった同分野に位置するフレームワークや開発ツールとは一線を画する認知度を持つようになってきました。

##価格体系・ライセンス
Titanium Mobileの利用に際しては無料で利用可能なApp EXPLOREライセンスと月額費用型のApp ACCELERATEライセンスが用意されています。App ACCELERATEにはStandard、Enhanced、Premiumの３種類のラインナップが用意されており、それぞれでサービスレベルが異なります。

いずれにおいてもTitanium SDKの利用や統合開発環境Titanium Studioの利用に制限はありません。

<table><tr><th colspan="2" rowspan="2"></th>
<th rowspan="2">App EXPLORE</th>
<th colspan="3">App ACCELERATE</th>
</tr><tr>
<th>STANDARD</th>
<th>ENHANCED</th>
<th>PREMIUM</th>
</tr>
<tr><th colspan="2">Appcelerator Analytics(解析機能)</th>
<td>5万イベント/月</td><td>10万イベント/月</td><td>20万イベント/月</td><td>30万イベント/月</td></tr>
<tr><th rowspan="4">Appcelerator<br />Cloud Sevices<br />の利用</th>
<th>プッシュ通知・メール送信</th>
<td>125万回/月</td><td colspan="3">無制限</td></tr>
<tr>
<th>Tier1API利用</th><td>25万回/月</td><td>50万回/月</td><td>100万回/月</td><td>150万回/月</td></tr>
<tr>
<th>Tier2API利用</th><td>25万回/月</td><td>50万回/月</td><td>100万回/月</td><td>150万回/月</td></tr>
<tr>
<th>ストレージ</th><td>5GB</td><td>10GB</td><td>20GB</td><td>30GB</td></tr>
<tr><th rowspan="5">サポート</th><th>対応方法</th><td>Webのみ</td><td>Web/Chat</td><td colspan="2">Web/Chat/電話</td></tr>
<tr><th>バグ対応</th><td>-</td><td colspan="3">致命的なバグ対応</td></tr>
<tr><th>対応時間</th><td colspan="2">平日8時〜17時（西海岸時間）</td><td colspan="2">毎日8時〜17時（西海岸時間）</td></tr>
<tr><th>レスポンス</th><td>ベストエフォート</td><td>2営業日内</td><td>1営業日内</td><td>8時間以内</td></tr>
<tr><th>advisory hours</th><td colspan="2">-</td><td>10時間/年</td><td>20時間/年</td></tr>
</table>

###2012年4月17日までのライセンスについて
Titanium Mobileには無料で利用可能なTitanium Communityライセンスと月額費用型のTitanium Indieライセンスが用意されています。
<table border="2">
<tr><th> </th><th>Titanium Community</th><th>Titanium
Indie</th><th>補足</th></tr>
<tr><th>月額費用</th><td>無料</td><td>59米ドル</td><td>開発者一人あたり</td></tr>
<tr><th>Titanium SDK</th><td>○</td><td>○</td><td>モジュール開発も可</td></tr>
<tr><th>α版使用</th><td>×</td><td>○</td><td>開発中機能を事前に使用できる</td></tr>
<tr><th>Titanium+Plus<br />
プロダクト利用</th><td>×</td><td>○</td><td>-</td></tr>
<tr><th>開発者掲示板</th><td>○</td><td>○</td><td>-</td></tr>
<tr><th>Moduleサポート</th><td>×</td><td>○</td><td>-</td></tr>
<tr><th>サポート対応時間</th><td>×</td><td>営業時間内</td><td>延長や現地時間には上位の契約にて対応</td></tr>
</table>

現実の開発をしている上で直接関係してくるのは、開発元によるサポートの有無になります。

ただ、開発者掲示板（掲示板型のQ&Aサイト）自体もAppceleratorが運営しており、そこで開発者間での相互扶助という形で問題解決の道が用意されています。開発者フォーラムにおいてもAppceleratorの開発者は回答していますが、回答や対応までの時間に関する保証がないという形になります。本書では無料のCommunity Editionで利用出来る範囲についてのみ触れていきますので、ご安心ください。

##「第二の選択肢」のリスク

2010年４月にあったiOS4の発表の折、開発者コミュニティで話題となったのがSection 3.3問題でした。SDKのダウンロード時に表示される規約文に次のような文言が記載されていたのです。

> アプリケーションはiPhone OSやWebKitで実行可能なObjective-C/C/C++あるいはJavaScriptによって記述されなければならない。
> また、これらのプログラミング言語で記述されたコードのみが定められたAPIへのアクセスを許可される。

なんらかの抽象レイヤーを介してAPIをコールするのではなく、Objective-Cで記述しなければAPIは呼び出してはいけないという、Titanium Mobileやこれに類する他の開発環境にとって致命的な規約となったのです。

これを受けてAppceleratorではビルド済みのライブラリとリンクしてJavaScriptを動作させるのではなく、Xcodeでも直接コンパイルできるプロジェクトとソースを一旦出力し、それをビルドさせるというアプローチを取ることで、規約を回避しようとしました。現時点でこの回避策が規約をクリアするものであったかは判断できませんが、同年９月にAppleが規約の上記内容を撤回したことにより、結果的にウヤムヤになっているというのが現状です。
しかしながら、撤回される以前から現在に至るまでTitanium Mobileを利用したアプリケーションがAppStoreには登場しつづけているという事実もあり、Titanium Mobileを利用することで申請が却下（Reject）されるような不利益の原因にはつながってはおりません。

とはいえ、ここで触れた規約問題のように、開発環境の存在自体を脅かすような状況が今後も発生しないという保証はありません。
Titanium Mobileに限らず、プラットフォーム提供者が想定する環境（iOS端末の場合はXcode + Objective-C/C/C++ 環境、Android端末の場合はJava）以外での開発を選択するということのリスクは、あくまでも開発者自身が負うことを十分に認識して利用する必要がありますので、その点をご留意ください。
