第三章 Hello, TiWorld（２）ソース解説
========================================================
先ほど動かしてみたソースについて中身を追いかけていきたいと思います。

##プロジェクトのフォルダ構成
<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ProjectFolderLayout.png" align="right" />
次のようなファイルやフォルダが生成されています。
代表的なものについて、簡単に説明します。

- build/ 
    ビルド結果を格納するフォルダ(対象のプラットフォームごとに出力先が異なる)
    直接触る必要性はありませんが、ビルドに失敗することがあったときにはこの中身を一度削除することで解決することもあります。（「フルビルド」のやりなおし）

	-  android/

	-  iphone/

	-  mobileweb/

- manifest
    パッケージングする際に用いるアプリケーション定義が記述されています。（基本的に変更しません）

- Resources/
    アプリケーション開発を用いるソースやリソースはここに格納します。

	- app.js

		アプリケーションの開始点（エントリポイント）となるJavaScriptスクリプトファイルです。

	- android/
	- iphone/
	- mobileweb/

		Android/iOS/Web向けに依存したソースや画像のリソースを格納します。
		ビルド時にResourcesにマージされ、機種依存を吸収される。

##app.jsを読む…前に
アプリケーションの開始点（エントリポイント）となるapp.jsですが、その内容を説明する前に、その中で関数（メソッド）の引数として度々登場している**JavaScriptオブジェクトリテラル**という表現について先に触れておきます。

```JavaScript
// {と}で囲っている部分が"オブジェクト"として扱われるため"オブジェクトリテラル"と呼ぶ
var theObject = {
	numericPropName: 10,
	stringPropName: "文字列",
	objectPropName: {
		nestedPropName: 123
	}
};
// 変数名とプロパティ名をドット区切りで指定することで値を取得できる。
alert(theObject.numericPropName); // 10
alert(theObject.objectPropName.nestedPropName);	// 123
alert(theObject["stringPropName"]);	// このような表現も可能（「文字列」と表示される）
```
ひとまずは、他の言語系で連想配列・ハッシュテーブル・ディクショナリと呼ばれるものと同じように捉えていただいて結構です。 
これを踏まえて、app.jsのソースを見ていきましょう。

##app.jsを読む

```JavaScript
// this sets the background color of the master UIView (when there are no windows/tab groups on it)
Titanium.UI.setBackgroundColor('#000');

// create tab group
// 各種オブジェクトの生成にcreateXXXというファクトリメソッドを用います。（newは使用しない）
var tabGroup = Titanium.UI.createTabGroup();


//
// create base UI tab and root window
//
// タイトルが「Tab 1」、アイコンを指定し、対応するWindowとしてwin1を指定したTabを生成し、tab1と命名。
var win1 = Titanium.UI.createWindow({  
    title:'Tab 1',
    backgroundColor:'#fff'
});
// タイトルが「Tab 1」背景色が白のWindowを生成し、win1という名前を付けています。
var tab1 = Titanium.UI.createTab({  
    icon:'KS_nav_views.png',
    title:'Tab 1',
    window:win1
});
// 表示内容「I am Window 1」表示色を灰色、フォント名とフォントサイズを指定したLabelを生成し、label1と命名。
var label1 = Titanium.UI.createLabel({
	color:'#999',
	text:'I am Window 1',
	font:{fontSize:20,fontFamily:'Helvetica Neue'},
	textAlign:'center',
	width:'auto'
});
// win1の内部にlabel1を配置する。
win1.add(label1);

//
// create controls tab and root window
//
var win2 = Titanium.UI.createWindow({  
    title:'Tab 2',
    backgroundColor:'#fff'
});
var tab2 = Titanium.UI.createTab({  
    icon:'KS_nav_ui.png',
    title:'Tab 2',
    window:win2
});

var label2 = Titanium.UI.createLabel({
	color:'#999',
	text:'I am Window 2',
	font:{fontSize:20,fontFamily:'Helvetica Neue'},
	textAlign:'center',
	width:'auto'
});

win2.add(label2);



//
//  add tabs
//
// tabGroupの内部にtab1とtab2を配置する。
tabGroup.addTab(tab1);  
tabGroup.addTab(tab2);  


// open tab group
// tabGroupを開く（表示する）。
tabGroup.open();
```

コードではUI構築しかほぼ行っていません。addやaddTabでUIの構築することにより親＞子＞孫＞…といった階層構造なっています。

	label1	label2
	win1	win2
	tab1	tab2
	tabGroup
	アプリケーションの根底（MasterUIView）

受け皿になるオブジェクトを「コンテナ」と呼びます。
app.jsの例では「win1はlabel1のコンテナとなっている」という風にいいます。

さて、画面上の部品としてTabGroup、Tab、そしてWindowというものが登場しました。
画面上の構成物として、視覚的には次の部分になります。
￼

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/WindowTab1.png" height="300" width="200" /> <img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/WindowTab2.png" height="300" width="200" />


Tab1・Tab2とアイコンと見出し付きで表示されているのがそれぞれTabと呼ばれるものであり、これを格納している黒のエリアがTabGroupです。
Tabを選択すると、それに紐づくWindowに切り替わります。

Windowはコンテンツを表示するユーザインタフェイスの中核部分になります。
Window上にはコンテナとして複数のViewもしくはコントロールが配置(add)もしくは除去(remove)できる他、Viewやコントロール自体もコンテナとなることが可能です。Window内に深い階層構造が発生するため、画面設計時にその親子関係を把握しておく必要があります。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/WindowTab3.png" height="300" width="200" />

	Tab	Window	NavBar	Button×２個	
	MapView	Annotation	ImageView
	Toolbar	Button×7個

##プラットフォームによるUIの違い

すでに画面を見ていただいているためにお気づきかもしれませんが、タブの選択部分がiOSでは下部に、Androidでは上部に表示されるようになっています。

これはネイティブのUI部品のデザイン思想・戦略の違いから生まれるものです。

この問題の解決策として、端的に言うとプラットフォームごとに処理を分けるしかありません。

もちろん同様の挙動をするようにUI構築することは不可能ではありませんが、実際にその画面を利用するユーザの視点に立つと、AndroidなのにiOSライクな動きをすること（あるいはその逆）は直感的な操作を阻害する要素以外の何者でもありません。
ゲームなど全画面のUIをすべて描画する系統のアプリケーションでは無い限り、プラットフォームの流儀に沿った造りになるように心がける必要があるでしょう。
￼また、Androidでは画面サイズ・解像度が異なる多数存在するため、すべての端末で意図する表示をさせるためにはかなりの労力を割く必要があります。

##コラム："Write once, adapt anywhere"という考え方
iOSとAndroidの両対応というと、かつてJavaが標榜していた**"Write once, run anywhere"**を思い出す方もいらっしゃるかもしれませんが、Titanium Mobile開発元のAppceleratorとしてはそこを目標としていません。

あくまでも書いたコードをどこにでも適用できるところをゴールとしており、プラットフォーム個別の実装を要する点（UIだけに限らず、プラットフォームに特化したような機能を利用する事も含め）はプラットフォーム差を埋めるコードを開発者サイドで書く必要があるということを念頭に置いておかないと、思いもしないところで躓いてしまいます。
