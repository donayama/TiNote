#シングルコンテキストかつCommonJSなTiサンプルを読む(draft)
>ひとまず草稿版です。ツッコミがあればこそこそ直して行きます(^^;

Titanium Mobile 2.0からAPIデモアプリであるKitchenSinkがついにというか、ようやくCommonJSアプリになり、公的なサンプルとしてTitanium 0.9からの伝統のマルチコンテキストモデルがなくなるという事態となりました。
拙著をものした時期というのがCommonJS化が加速する直前のTitanium Mobile 1.6.xのころで、今となっては目も当てられない状態になってしまっているのですが、それはさておき。
シングルコンテキストかつCommonJS時代を迎えたTiの初期ソースはどうなるのかを見てきたいと思います。

https://github.com/appcelerator-developer-relations/Template.SingleWindow

##ファイル構成
これまでapp.jsとKS_nav_ui.png, KS_nav_views.pngだけという極めてシンプルだったのと対照的に実践的かつ若干敷居の高いものになっているように思われます。

* Resources/
	* ui/
		* common/
			* FirstView.js
	* handheld/
		* android/
			* ApplicationWindow.js
		* ApplicationWindow.js
	* tablet/
		*ApplicationWindow.js
	* app.js
* i18n/
	* en/
		* strings.xml
	* ja/
		* strings.xml

あれこれたくさんあってややこしく見えますが、特定のパターン（といってもiPhoneでの利用という比較的スタンダードなケースですが）で使う部分だけをフィルタしてみると次のようになります。案外シンプルなのではないかと思います。

* Resources/
	* ui/
		* common/
			* FirstView.js
		* handheld/
			* ApplicationWindow.js
	* app.js
* i18n/
	* ja/
		* strings.xml

それでは個々のファイルについてみてみましょう。

##Resources/app.js
従来からのTitaniumの動きと同様にアプリケーションのエントリポイントとして機能します。
先頭のコメントのみを割愛して引用します。

```javascript:app.js
//bootstrap and check dependencies
if (Ti.version < 1.8 ) {
	alert('Sorry - this application template requires Titanium Mobile SDK 1.8 or later');	  	
}

// This is a single context application with mutliple windows in a stack
(function() {
	//determine platform and form factor and render approproate components
	var osname = Ti.Platform.osname,
		version = Ti.Platform.version,
		height = Ti.Platform.displayCaps.platformHeight,
		width = Ti.Platform.displayCaps.platformWidth;

	//considering tablet to have one dimension over 900px - this is imperfect, so you should feel free to decide
	//yourself what you consider a tablet form factor for android
	var isTablet = osname === 'ipad' || (osname === 'android' && (width > 899 || height > 899));

	var Window;
	if (isTablet) {
		Window = require('ui/tablet/ApplicationWindow');
	}
	else {
		// Android uses platform-specific properties to create windows.
		// All other platforms follow a similar UI pattern.
		if (osname === 'android') {
			Window = require('ui/handheld/android/ApplicationWindow');
		}
		else {
			Window = require('ui/handheld/ApplicationWindow');
		}
	}
	new Window().open();
})();
```

色々長ったらしいですが、書いてあることをまとめると以下のようになります。

* Titanium Mobile 1.8未満だと警告メッセージを表示する
* Platformを判定するためにOS名とバージョン、デバイスの解像度の取得
* 種別としてTablet端末なのか、はたまたAndroid端末なのか、それ以外の携帯デバイス(iOS系)なのかを判断し、それぞれに特化したApplicationWindowを取得する
* 取得したApplicationWindowをnewして生成したオブジェクトを表示（open）する
というところで、単に判定もせずにApplicationWindowのオブジェクトを開くだけなら次のようなコードになってしまいます。

```javascript
(function() {
 	var Window = require('ui/handheld/ApplicationWindow');
 	new Window().open();
})():
```

###即時実行関数ブロック
何事も無いかのようにさらっと登場している次のような表現。

```javascript
(function() {
    /* なんらかの処理 */
})():
```

これはなにかと聞かれると `function(){…}` で定義された無名関数をその場で評価している即時実行関数などと呼ばれる手法です。
この手法のメリットは名前空間の汚染の最小化でしょうか。

以下のように書いても、上記の処理と同等に動きますが、実行されているアプリケーション内のグローバル変数として、Windowが居続ける形になってしまいます。

```javascript
var Window = require('ui/handheld/ApplicationWindow');
new Window().open();
```

JavaScriptの変数スコープは関数内で閉じられるため、即時実行関数を介する事で、Windowはapp.js内の `(function(){…})` の外から参照できない状態となります。

もちろん参照できる必要がある局面もあると思いますが、それはまた後述したいと思います。

##require関数でCommonJSモジュールを呼び出す
もう一つ何も説明無くつかっているのがreuqireという関数です。
これまで他のJSファイルを読み込む必要がある婆、方式としてこのrequire以外に２種類ありました。

* Titanium.UI.createWindow({url: '〜.js'})
* Titanium.include('〜.js')

createWindowでのurl指定はマルチコンテキストモデルの呼び出し方であり、現時点でAppceleratorはこの方式を推奨しておりません。むしろ「特殊なケース」でしか使わないようにという指針を出しています。
include自体も積極利用を謳っておらず、Appceleratorとしては開発者にこれまでの作り方を一旦リセットしてCommonJSモジュールを用いたコードモデルに移行してほしいという考えのようです。

そのCommonJSモジュールのスクリプトを読み込むために用いるのがrequireという関数です。

```javascript
var Window = require('ui/handheld/ApplicationWindow');
```

ここでは `Resources/ui/handheld/ApplicationWindow.js` を読み込み、その結果となるクラス的なオブジェクト((なんと表現したらいいんだろう？))をWindowという変数にセットしているという形です。

実行ファイル空間ではResourcesフォルダがアプリケーションディレクトリの頂点にあるため、そこからの相対参照のファイル名を引数として指定します。
ここで注意したいのが拡張子の.jsが省略されている点です。ついつい書いてしまうとハマります。

ちなみにrequire自体はオブジェクトを返す関数なので、こういう書き方もできなくもありません。コードの可読性や保守性と照らし合わせて、使用をご検討ください。
>|javascript|
new require('ui/handheld/ApplicationWindow').open();
||<

##Resources/ui/handheld/ApplicationWindow.js
それではapp.jsからrequireされているApplicationWindow.jsの中身を見てみましょう。
これがCommonJSモジュールのサンプルとも言えます。

```javascript
//Application Window Component Constructor
function ApplicationWindow() {
	//load component dependencies
	var FirstView = require('ui/common/FirstView');

	//create component instance
	var self = Ti.UI.createWindow({
		backgroundColor:'#ffffff'
	});

	//construct UI
	var firstView = new FirstView();
	self.add(firstView);

	return self;
}

//make constructor function the public component interface
module.exports = ApplicationWindow;
```

中身も至ってシンプルなのですが、順に追っかけて行きたいと思います。

まずコードの構造ですが、

```javascript
function ApplicationWindow() {
	var self = Ti.UI.createWindow({/* params */});
	return self;
}
module.exports = ApplicationWindow;
```

* ApplicationWindowというクラスコンストラクタ的な関数を宣言
	* Ti.UI.Windowのオブジェクトselfを返す関数。
* ApplicationWindow関数をmodule.exportsという値に設定

という２段構えになっています。

ここで重要になるのがこのスクリプトファイル内での変数スコープが閉じている（カプセル化される）ということ、そしてmodule.exportsに指定されたオブジェクトのみが外部に対して開放されるという点です。

たとえば次のようなスクリプトがあったとします。

```javascript
function Func1() {
    alert("1");
}
function Func2() {
    alert("2");
}
function Func3() {
    alert("3");
}
function FuncAll() {
    Func1();
    Func2();
    Func3();
}
module.exports = FuncAll;
```

このモジュールを読み込んだ側として直接利用できるのはmodule.exportsで指定されているFuncAllの処理だけになります。もちろんFuncAllを実行すると、内部的にはFunc1〜3の処理を参照できるのでalertが順に表示されます。

また、Ti.includeを実行した際には読み込んだ側の変数スコープに組み込まれたため、たとえばapp.jsでFunc1()を実行することも可能でしたが、そういったことは出来ません。

ある意味Ti.includeの時代は「おおらか」であり、CommonJSは敷居が高く、締め付けが厳しいと感じられるかもしれません。
しかし、一発ネタではない程度の規模のアプリケーション構築を進めて行くなかでUIや処理の単位で変数スコープを厳密に管理しながら開発する上でCommonJSスタイルに切り替えたメリットを享受できるのだと信じてください(^^;
三つ子の魂百までというコトバもあるとおり入門時に染まった安易なやり方というのはなかなか脱却できませんので、可能な限りCommonJSにswitchするようにしましょう。

さて、ApplicationWindow.jsの全体像をもう一度振り返ってみます(コメントはカットします)。

```javascript
function ApplicationWindow() {
	var FirstView = require('ui/common/FirstView');

	var self = Ti.UI.createWindow({
		backgroundColor:'#ffffff'
	});

	var firstView = new FirstView();
	self.add(firstView);

	return self;
}
module.exports = ApplicationWindow;
```

コード構造の際に説明してなかった点としてApplicationWindow関数の実装があります。
次のような流れです。

* Resources/ui/common/FirstView.jsをrequireし、変数FirstViewに設定
* Ti.UI.createWindowでTi.UI.Windowオブジェクトを生成し、変数selfに設定
* FirstViewをインスタンス化したfirstViewをTi.UI.Windowに追加(add)
* 変数selfをreturnする

Window内にFirstViewというものを組み込む入れ子のUI構造にして表示を行うのですが、そのFirstView自体もCommonJSモジュールになっているという寸法です。

このApplicationWindow自体はTi.UI.Windowのオブジェクト生成をおこなうための処理であり、Windowを開いたりすることはApplicationWindow側のお仕事になっている点に注意したいところです。

##Resources/ui/common/FirstView.js
同じくFirstViewの中身を見てみましょう。

```javascript:FirstView.js
//FirstView Component Constructor
function FirstView() {
	//create object instance, a parasitic subclass of Observable
	var self = Ti.UI.createView();

	//label using localization-ready strings from <app dir>/i18n/en/strings.xml
	var label = Ti.UI.createLabel({
		color:'#000000',
		text:String.format(L('welcome'),'Titanium'),
		height:'auto',
		width:'auto'
	});
	self.add(label);

	//Add behavior for UI
	label.addEventListener('click', function(e) {
		alert(e.source.text);
	});

	return self;
}

module.exports = FirstView;
```

構造自体は先ほどのApplicationWindowと同様なので、FirstView関数の中身だけを追いかけます。

```javascript
	//create object instance, a parasitic subclass of Observable
	var self = Ti.UI.createView();

	//label using localization-ready strings from <app dir>/i18n/en/strings.xml
	var label = Ti.UI.createLabel({
		color:'#000000',
		text:String.format(L('welcome'),'Titanium'),
		height:'auto',
		width:'auto'
	});
	self.add(label);
```

Viewというパネルオブジェクトを生成し、その中にLabelオブジェクトを配置しています。
ApplicationWindowのときと同様にcreateXXXに対しては以下のようなJavaScriptオブジェクトリテラルと呼ばれる連想配列的な指定方法で初期値設定が行えます。

```javascript
	{
		color:'#000000',
		text:String.format(L('welcome'),'Titanium'),
		height:'auto',
		width:'auto'
	}
```

ここでは色は黒色(#000000)で、高さと幅を自動調節した文字列textを表示するのですが、textの値として次のように指定されています。

```javascript
String.format(L('welcome'),'Titanium')
```

この `L` という関数は地域化対応(L10N)を行うための仕掛けで、指定されたキー文字列（ここでは `'welcome'` ）に基づき i18n/xx/strings.xmlを照会し、置き換え処理を行います。

```xml:strings.xml
<?xml version="1.0" encoding="UTF-8"?>
<resources>
	<string name="welcome">%sへようこそ!</string>
</resources>
```

と指定されているので、ja(日本語)設定の端末では次のように解釈されます。

```javascript
String.format("%sへようこそ!", 'Titanium')
```

String.formatというAPIは%sを引数で置き換える処理をするので「Titaniumへようこそ!」という文字列がLabelに設定される事が分かると思います。

最後にUIに関するイベント処理としてクリック時イベントの定義がされています。
クリックするとe.source、すなわちクリックされたlabel自体のtextプロパティ（「Titaniumへようこそ!」）をalert表示するというだけの内容です。

```javascript
	//Add behavior for UI
	label.addEventListener('click', function(e) {
		alert(e.source.text);
	});
```

このように単に「Titaniumへようこそ!」と表示されるだけのアプリでも以前のようなapp.js1ファイルだけでどうにかするという方法ではなく、徹底的にモジュール化された構造をテンプレートとして用意してきています。
気軽さという観点ではちょっと敷居が高くなったなあと思いますが、前述の通り入り組んだアプリ構築の際には必ず役に立ってくるはずなので、思い切ってSwitchを試みてはいかがでしょうか。
