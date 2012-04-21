#Titanium Mobile Best Practices
この章はAppcelerator公式Wikiのドキュメントに基づき、和訳±αしたものです。
>
http://docs.appcelerator.com/titanium/2.0/index.html#!/guide/Mobile_Best_Practices

Titanium Mobileは比較的歴史浅く、継続的に変化をしているプラットフォームです。
このベストプラクティスは変更される可能性も否めませんし、Titaniumのアプリケーションを構築するのに必要な知識のすべてを網羅する事も出来ませんので、現在Titanium Mobileアプリケーション開発、もしくは一般的なJavaScript開発において「良い」（そしていくつかの「悪い」）として知られているプラクティスを整備することがこのガイドラインの目的となっています。

その前提で、この章で扱うプラクティスのアジェンダをまとめると以下のようになります。

1. グローバルオブジェクトを汚染しない
2. シングルコンテクストで実行する
3. CommonJSモジュールを使い、モジュール／オブジェクト指向とする
4. 必要になるまでJSファイルの評価を遅延させる
5. メモリ管理には適切な手段を用いて、メモリリークを防ぐ

「えっ、それだけなの？」という声もあるかもしれません。
たとえばMVCやMVVMといったアーキテクチャや、既存・独自に構築したフレームワークを使用してアプリケーション開発するとしても、アプリケーション構造、ビジネスロジック、あるいは規約は異なる場合があるので、個々のプロジェクトに万能にフィットするようなものを選びせるものではありません。
このドキュメントの目的はあくまでも合理的かつ、高いパフォーマンスのTitanium Mobileアプリケーションを開発するための基盤となる部分、に限定してまとめたいと考えています。

##ガイドライン その1 グローバルオブジェクトを汚染しない
JavaScriptではアプリケーション全体で使用できるグローバルオブジェクトが存在し、グローバルな名前空間で定義された変数はグローバルオブジェクトのプロパティとなります。
Titanium Mobileにおいてグローバルオブジェクトとはすなわちapp.jsで定義されたものがこれにあたります。

app.jsで宣言された変数はアプリケーション全体で利用可能でなければなりません。
Ti.includeを介して外部スクリプトが取り込まれた際に、変数名の衝突を引き起こす可能性があります。また、これとは他の理由でTi.includeの使用は推奨されておらず、CommonJSのモジュールの仕様に乗っ取ったrequire関数によって将来的に置き換えられる予定です。

app.jsのグローバルオブジェクトを汚染しないようにするには、JavaScriptで利用可能な唯一のスコープ(クロージャ)を使用する必要があります。グローバルスコープを汚染を避けるためにクロージャを使用する簡単な方法として挙げられるのが、自己呼出関数いわゆる「即時関数」呼出しです。


```JavaScript
(function() {
  var privateData = 'tee hee! can\'t see me!';
})();
alert(privateData); //undefined in the global scope
```

外部ファイルにコードを分離するときは、CommonJS仕様に従って提供されるrequire関数によって、グローバルスコープまたはモジュールのスコープに機能を追加するべきです。この手法については、後ほど説明します。

##ガイドライン その2 シングルコンテクストで実行する
ガイドラインその１にてJavaScriptアプリケーションにおけるグローバルスコープについて述べましたが、Titanium Mobileアプリケーションにおいてはこのグローバルスコープをurlプロパティを持つウィンドウの単位で管理する事が可能です。
Windowを開く関数(Openメソッド)が呼び出されるとURLで指定されたJavaScriptの評価時に新しい変数スコープを作成します。カスタムイベントの使用による場合を除いて、お互いの変数を参照する事ができません。これが「実行コンテキスト」と呼ばれる複数稼働するJavaScript環境にあたります。

しかし、本当に一部のケースを除いて、これは「悪」と考えることができます。

なぜかというと、複数の実行コンテキストによって循環参照が引き起こされ、メモリリークが発生する可能性があるからです。この他にも、JavaScriptコードの評価時にライフサイクルが不明瞭になる問題があります。
コンテキストの間でデータの共有をする際にも、アプリケーションレベルでのイベントを通して行うなど、不格好な方法を用いなければなりません。

新しいウィンドウごとに「キレイな」グローバルコンテキストが欲しいという需要は潜在的にはあるでしょう。
例えばデータ共有が不要であるKitchenSinkのようなアプリ内のアプリといった状況では役に立つかもしれませんが、やはりこれは稀なケースであるため、可能な限りURLプロパティを設定したWindowを使用しないようにしましょう。

##ガイドライン その3 CommonJSモジュールを使い、モジュール／オブジェクト指向とする
関心の分離によって、アプリケーションロジックごとに、[凝集度（Cohesion）](http://ja.wikipedia.org/wiki/%E5%87%9D%E9%9B%86%E5%BA%A6)の原則を忠実にまもったJavaScriptファイルやオブジェクトへの分割されるべきです。

すべてのJavaScriptアプリケーションがインスタンス化オブジェクトを使用しなければならないわけではありませんが、少なくとも、CommonJSモジュールの規格に則るべきです。
CommonJSモジュールの仕様により、モジュールファイル内でexportsオブジェクトを介して外部に提供されるオブジェクトがサンドボックス化されます。

Titanium MobileにおけるCommonJSモジュールの使用例は以下の通りです。

###シンプルなCommonJSの例 

####`Resources/lib/maths.js`


```JavaScript
var privateData = 'private to the module\'s sandbox, not available globally';
 
//any property attached to the exports object becomes part of the module's public interface
exports.add = function() {
  var result = 0;
  for (var i = 0, l = arguments.length;i<l;i++) {
    result = result+arguments[i];
  }
  return result;
};
```

####`Resources/app.js`


```JavaScript
var maths = require('lib/maths');
var sum = maths.add(2,2,2,2,2);
//sum is 10
```

###CommonJSモジュールでインスタンス化可能なオブジェクトの例

####`Resources/lib/geo.js`


```JavaScript
function Point(x,y) {
  this.x = x;
  this.y = y;
}
 
function Line(start,end) {
  this.start = start;
  this.end = end;
}
 
//create public interface
exports.Point = Point;
exports.Line = Line;
```

####`Resources/app.js`


```JavaScript
var geo = require('lib/geo');
 
var startPoint = new geo.Point(1,-5);
var endPoint = new geo.Point(10,2);
 
var line = new geo.Line(startPoint,endPoint);
```

Titanium Mobileアプリケーションにおいてオブジェクトを作成するとき、オブジェクト指向を含む一般的なプログラミングのベストプラクティスに準拠する必要がありますが、ここではもう一つの方法について論じます。

Titanium Mobileにおける一般的なニーズとしてカスタムUI部品を作るというものがあります。組み込みオブジェクトに拡張するための一般的な方法は、**Parasitic Inheritance**（他のオブジェクトへコピーし、メソッドやプロパティを追加する方法）ですが、標準的なJavaScriptでは妥当なプラクティスである一方、Ti.UI.createViewなどで返されるオブジェクトに代表される**Proxyオブジェクト**(ネイティブAPIで表されるがTitanium MobileのJavaScriptブリッジの機構によりJSのオブジェクトとして「見えている」オブジェクトを指す。具体的にはネイティブUI全般など。)を使用する際には予期しない動作をする可能性があります。 
カスタム部品を作成するために合理的なアプローチは、通常のJavaScriptオブジェクトとProxyオブジェクトを関連付ける、次のような形となります。

###カスタムチェックボックスの例

####`Resources/ui/ToggleBox.js`


```JavaScript
function ToggleBox(onChange) {
  this.view = Ti.UI.createView({backgroundColor:'red',height:50,width:50});
 
  //private instance variable
  var active = false;
 
  //public instance functions to update internal state, execute events
  this.setActive = function(_active) {
    active = _active;
    this.view.backgroundColor = (active) ? 'green' : 'red';
    onChange.call(instance);
  };
 
  this.isActive = function() {
    return active;
  };
 
  //set up behavior for component
  this.view.addEventListener('click', function() {
    this.setActive(!active);
  });
}
exports.ToggleBox = ToggleBox;
```

###Resources/app.js


```JavaScript
var win = Ti.UI.createWindow({backgroundColor:'white'});
var ToggleBox = require('ui/ToggleBox').ToggleBox;
 
var tb = new ToggleBox(function() {
  alert('The check box is currently: '+this.isActive());
});
tb.view.top = 50;
tb.view.left = 100;
 
win.add(tb.view);
```

##ガイドライン その4 必要になるまでJSファイルの評価を遅延する

Titaniumアプリケーションにおける大きなボトルネックの一つとして「JavaScriptスクリプトの評価」が挙げられます（(特にAndroidにおいては。ただし、これはRhinoからV8ランタイムへの移行によりかなりマシになるはずですが…）
そのため、アプリケーションの起動速度やレスポンスを高速化するために、開発者はそれらが絶対に必要になるまでスクリプトをロードしないようにするべきです。

次の例では３つのウィンドウがそれぞれクリック（タッチ）イベントによって開くようになっています。必要になるまでJavaScriptファイルがロードされていないことに注目してください。

###`Resources/app.js`


```JavaScrippt
//起動時に必要
var WindowOne = require('ui/WindowOne').WindowOne;
 
var win1 = new WindowOne();
win1.open();
 
win1.addEventListener('click', function() {
  // win2に必要なオブジェクトをロード
  var WindowTwo = require('ui/WindowTwo').WindowTwo;
  var win2 = new WindowTwo();
  win2.open();
  win2.addEventListener('click', function() {
    // win3に必要なオブジェクトをロード
    var WindowThree = require('ui/WindowThree').WindowThree;
    var win3 = new WindowTwo();
    win3.open();
  });
});
```

##ガイドライン その5 メモリ管理には適切な手段を用いて、メモリリークを防ぐ
割り当てられたメモリリソースの開放をするるには二つの選択肢があります。

一つ目の方法として、現在開いているウィンドウを閉じることです。
これによりウィンドウ内のビューオブジェクトに関連付けられたリソースを解放するようになります。

二つ目の方法は、プロキシオブジェクトへの参照にnullを設定することです。
オブジェクトのガベージコレクション時に、関連するネイティブオブジェクトも同様にクリーンアップされます。

```JavaScript
//Nulling out object references
var win = Ti.UI.createWindow();
 
var myBigView = new BigView();
win.add(myBigView.view);
win.open();
 
//...なんやかんやがあったあと...
win.remove(myBigView);
myBigView = null; // view(myBigView.view)がGCされるようになる
```

