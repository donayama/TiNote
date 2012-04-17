<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/cover.png" height="240" />

第四章 KitchenSink
==========================================================
それぞれのUI部品の動作を見るためには「KitchenSink」というデモアプリがおすすめです。
「KitchenSink」（キッチンシンク）とはAppceleratorから提供されているTitanium Mobileの機能をカテゴリ別を個別に紹介したカタログのようなデモアプリケーションです。 

ほぼすべての機能を全部入りにしている1ため、KitchenSinkでの動作を知っていればTitanium Mobileでの開発で「こういう動きをしたい」という要件とKitchenSinkの該当部分（とそのソース）がリンクできるようになります。

APIリファレンスに記載されていない機能もテストを兼ねて実装されていたりするため、KitchenSinkの実装コードを実際のアプリケーションにそのまま引用し、そこに修正を加えるほうが早いことも多いです。

##KitchenSinkを動かす
ビルド済みのKitchenSinkはAppStoreで公開されておりません。残念ながらデモアプリが配布禁止であるためです。
しかし、KitchenSinkも他のAppcelerator製品同様にソースコードがgithub上で公開されているオープンソースソフトウェアのひとつです。

###TitaniumStudioからgitのcloneを行う
このgithubからのソースのインポートも簡単にTitanium Studio内から行う事ができます。

まずはSample Viewを表示します。Windowメニューから```Show View```-```Other…```を選択し、```Samples```を選択します。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioSampleViews1.png" />
<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioSampleViews2.png" />

そうすると画面下部のTasks/Consoleなどが並ぶエリアにSamplesが追加されますので、ここに表示されている```Kitchen Sink```を右クリックし、```Import sample as project…```を選択します。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioSampleViews3.png" />

プロジェクトのインポート画面が表示されるので、適当なプロジェクト名（KitchenSinkのままで良いと思います）を選択して、Finishボタンをクリックすると、githubからファイルを受信し、新たなプロジェクトが生成されます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioKitchenSink1.png" />

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioKitchenSink2.png" />

###githubから取得する
githubから直接取得する場合は次の手順となります。

レポジトリのスナップショットを取得だけをする場合 gitクライアントは不要です。 
上記リンクにアクセスし、画面の右上にある「ダウンロード」ボタンを選択します。 続いて表示される画面でアーカイブファイルを選択し、ダウンロードします。完了後、展開してください。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioKitchenSink3.png" />
 ￼
展開したKitchenSinkのアーカイブから最新のプロジェクトをTitanium Studioに取り込みます。 Titanium Studioのファイルメニューから「Import」を選択します。
￼
<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioKitchenSink4.png" />

候補からImport Existing Titanium Projectを選択し、必要なパス情報を入力すると取り込まれます。先ほど展開した先の /KitchenSink フォルダを選択後「OK」ボタンをクリックします。
そうするとKitchenSInkプロジェクトがプロジェクト一覧に追加されます。

あとはHello Worldアプリケーションと同様にエミュレータもしくは実機で動作させるだけです。

###ビルドできない状態の回復
Gitからのインポートやこのような既存のTitaniumプロジェクトの取り込み時には自動的にTitanium Mobile(もしくはDesktop)のプロジェクトと認識されないことがあります。
その場合、ProjectメニューのPropertiesを選択し、Project Naturesで次の画面のようにTitanium Mobile（とWeb）が有効になるようにしてください。
￼
<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/TiStudioKitchenSink5.png" />

##KitchenSinkの構成
KitchenSinkは５つのタブからなっており、デモの性質が異なります。
BaseUI	UIインタフェイス（WindowやViewといったコンテナとなるUI）
Controls	コンテナに載るUI部品
Phone	デバイス機能に特化した機能デモ（音声・動画再生、カメラ、アドレス帳）
Platform	OSに搭載された機能を用いるデモ（ネットワーク通信・データベースなど）
Mashups	RSS FeedやFacebook、Twitter APIなどのリモートデータを利用したデモ

なお、iOSシミュレータなどのエミュレータ上ではハードウェアに依存する処理（カメラ・加速度センサ・電子コンパスなど）がサポートされていないため、動作しません。
すべての機能を知るにはやはり実機に転送する必要がありますが、iOSでは実機検証するために有償のiOS Developer Programに参加する必要があります。
