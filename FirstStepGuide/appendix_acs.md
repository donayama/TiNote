<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/cover.png" height="240" />

Appendix. 写真投稿アプリを通じて学ぶ Appcelerator Cloud Service 最初の一歩
==========================================================
#Appcelerator Cloud Service(ACS)とは何か？

Appceleator Cloud Serviceと呼ばれるサービスがTitanium Mobile 2.0からサポートされるようになりました。
そもそもこのサービスはなんなのか？というと、BaaS(Backend as a Service)と呼ばれる分野のサービスがあり、その選択肢のひとつとなっています。

>
BaaSとは一般に、モバイルアプリケーションのバックエンドとして求められるデータストア機能、プッシュ通信機能、ユーザー管理機能、ソーシャルとの連係、ロケーションとの連係などを備え、それらの機能をモバイルアプリケーションからAPIで呼び出すことで、サーバ側のコードを書くことなく、クラウドと連動するモバイルアプリケーションを効率よく開発できる環境を提供するものです。

>>
モバイル向けの新クラウド、BaaS（Backend as a Service）とは何か。「Parse」が正式サービス開始: Publickey
http://www.publickey1.jp/blog/12/baasbackend_as_a_serviceparse.html

かつてCocoafishと呼ばれたサービスをAppceleratorが買収し、Appcelerator開発者アカウントと透過的に接合したサービスがACSと言えます。透過的に使用できるためリモートサーバの存在すら忘れてしまいそうになり、いわばTitanium Mobileによるモバイルアプリケーションの開発に集中できるツールとして活用できるのではないでしょうか。

上記のBaaSの解説にあるようにACSでは次のようなサービスが提供されます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSWebSite_Home2.png" />

これらの機能はREST APIとして提供されているものですが、これをTitaniumからアクセスしやすくするためのモジュールが準備されており、たとえば次のようなコードを記述するだけでローカルにあるphoto.jpgをサーバにアップロードできます。

```JavaScript
Cloud.Photos.create({
    photo: Titanium.Filesystem.getFile('photo.jpg')
}, function (e) {
    if (e.success) {
        var photo = e.photos[0];
        alert('Success:\\n' +
            'id: '         + photo.id       + '\\n' +
            'filename: '   + photo.filename + '\\n' +
            'size: '       + photo.size,
            'updated_at: ' + photo.updated_at);
    }
});
```

上記のコードからも分かるようにサーバサイドのストレージ管理、ユーザとの紐付け、さらには写真の各種サイズへのリサイズ処理などについて一切コーディングせずにあらかじめ提供されている機能を使う事でクライアントサイドのJS実装に集中できるというわけです。

##費用面とかもろもろ

ACSの公開にあわせて、これまでのTitanium Community EditionをはじめとするSubscriptionの体制が一新されました。

http://appcelerator.com/plans-pricing

これまでのCommunityにあたるのがApp EXPLOREですが、このEditionでもACSの利用は可能です。毎月の処理量に制限(いわゆるQuota)が設定されており、125万回のプッシュ通知（メール含む）、Tier1 Tier2のAPIコール（どのAPIがどのTierなのかが明示的ではないのですが…）がそれぞれ25万回、5GBのストレージという制限がついています。

ビジネスユースを想定すると少々厳しい設定値となっていますが、テスト的な目的には十分無料で使えるのではないでしょうか。

これ以上の利用に対しては有償利用となる３段階のグレードが用意されております。
いずれもプッシュ通知・メール送信は無制限となっており、APIコール回数とストレージ容量が異なります。（さらにいうとサポートレベルも変わります）

- App ACCELERATE STANDARD
    - APIコールがそれぞれ50万回、10GBストレージ
- App ACCELERATE ENHANCED
    - APIコールがそれぞれ100万回、20GBストレージ
- App ACCELERATE PREMIUM
    - APIコールがそれぞれ150万回、30GBストレージ

肝心の費用については今のところ「営業に聞いてくれ」というパターンですので、ちょっと不安が残りますね。

##ACSのために必要なもの

ACSを使用するのにあたって必要となるものは以下の通りです。

- Appcelerator Account
- Titanium Mobile 2.0
- Titanium Studio(使用しなくてもOK)

#サンプルアプリ開発

タイトルにもある通り、写真投稿アプリの基礎部分の開発を通して、ACSに触れていきたいと思います。

主な機能としては次の通りです。

- 認証処理
- 全ユーザの投稿されている写真を一覧表示する（写真情報とサムネイルの取得、ログインしなくても見える）
- 写真の投稿

次の機能はひとまず割愛しておきますが、ヒントはドキュメント内に書いておきますので、是非APIリファレンスを見ながら実践してみてください。
もしこの記事が好評なら、続編としてフォローします ;-）

- 新規アカウント作成
- 個別写真の拡大表示

##ACSにアプリケーション設定をする

まずはTitanium MobileアプリケーションからACSを使うための準備をします。

以下のURLのサイトにアクセスしてください。

http://cloud.appcelerator.com

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSWebSite_Home.png" />

右上のLoginボタンからログインをします。

ログインに成功すると、次のような画面が表示されます。
これは開発者アカウントに紐づくACSアプリケーションの一覧画面です。
ACS上には任意の管理名称を持つアプリケーションを設置する事ができ、各アプリケーション単位にデータが分離されて保存されます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSMyApps_Blank.png" />

###ACSコンソールからのアプリケーション作成

さっそくアプリケーションを割り当ててみましょう。
```Register a New App```ボタンをクリックすると、次の画面が表示されます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSRegisterANewApp1.png" />

アプリケーション名と説明文を記入して、```Register App```ボタンをクリックします。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSRegisterANewApp2.png" />

すぐさまデータ領域が割り当てられ、次の画面に遷移します。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSRegisterANewApp3.png" />

Settingタブなどを覗くと先ほど入力した設定も修正できることが分かります。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_Settings1.png" />

スクロールするとプッシュ通知の設定やSMTPサーバの設定といった欄も見つかります。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_Settings2.png" />

MyAppsの状態は次のようになっています。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSMyApps_Single.png" />


先ほどのDetails欄に表示されているOAuthの情報を控えて、手動でパラメータ設定…という手もあるのですが、実はもっと簡単な方法があるので、そちらをご紹介したいと思います。


###Titanium StudioからのACSアプリケーション作成

それはTiStudioのプロジェクト設定でやってしまうという方法です。

せっかくなので新規プロジェクトを作成してみます。
Fileメニューから```New```-```Project…```を選択して、次のように入力していきます。
過去のバージョンにはなかった**Cloud Settings**欄が追加されていますので、```Automatically cloud-enable this application```のチェックを付けたままプロジェクトを作成します。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSTiStudio1.png" />

作成直後にtiapp.xmlを開くと、過去のTiStudioのバージョンにはなかったCloud Servicesという枠が右下のほうに表示されており、APIキーが表示されています。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSTiStudio3.png" />

中身自体は次のようなかたちになっており、ACSを使う際のAPIキーだけではなく、OAuthに関する各種情報も含まれているだけではなく、末尾に**CommonJSモジュールであるti.cloudの使用宣言**がされている点にも注意が必要です。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ti:app xmlns:ti="http://ti.appcelerator.org">
    <property name="acs-oauth-secret-production" type="string">bnTyWF1v7u4jewGxOrRgtNZakPknQNHR</property>
    <property name="acs-oauth-key-production" type="string">3px1CJu4GlnxCtbu3p3VC6pSNWMrT5lB</property>
    <property name="acs-api-key-production" type="string">lb6JEAChmgzNgJCynyUYIE3BeZlttiAi</property>
    <property name="acs-oauth-secret-development" type="string">jmP6VlX4Og3WUvpGXgnixteKreKXkWUQ</property>
    <property name="acs-oauth-key-development" type="string">djmhUBP52yWm0B1ZEhmEvOK5GCci2i6S</property>
    <property name="acs-api-key-development" type="string">hIqMzy9OveXuI3t9tx9P3xdHNbKb9heF</property>
    <deployment-targets>
        <target device="mobileweb">true</target>
        <target device="iphone">true</target>
        <target device="ipad">true</target>
        <target device="android">true</target>
        <target device="blackberry">false</target>
    </deployment-targets>
    <sdk-version>2.0.0.GA</sdk-version>
    <id>jp.hsj.acstest2</id>
    <name>ACSTest2</name>
    <version>1.0</version>
    <!-- 略 -->
    <modules>
        <module platform="commonjs" version="2.0.0">ti.cloud</module>
    </modules>
</ti:app>
```

実際にこの状態で先ほどの http://cloud.appcelerator.com/ を開いてみましょう。
ACSTest2というTitanium Mobileプロジェクトに対して、-developmentと-productionというsuffixのついたACSTest2-developmentとACSTest2-productionという２つのアプリケーションが生成されています。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSMyApps_TiStudio.png" />


##ログイン処理

ACS接続の準備も出来たので、ACSTest2アプリケーションにログイン処理を実装してみたいと思います。

が、ログインするためのユーザが一切無い状態ですので、ACSのコンソールから手動でユーザアカウントを作成してみます。

###ACSコンソールでのアカウント作成

MyApps画面からACSTest2-developmentを選択し、Manage Dataタブの下方にあるメニューから```Show/Edit Users```を選択します。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_CreateUser1.png" />

ユーザが存在すればユーザアカウントの一覧が表示されるべき画面なのですが、現時点は未設定なので```Create a User```ボタンしかありません。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_CreateUser2.png" />

```Create a User```ボタンをクリックすると、入力ウィンドウが表示されますので、適当にデータを入れてみます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_CreateUser4.png" />

ユーザ個別の写真割当のほか、ユーザに対して個別に設定できるカスタムフィールド機能もACSには存在しています。
データ名称・データ型も含めて指定でき、任意の値が設定できます。ここではtwitterのアカウントを設定しているイメージにしています。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_CreateUser6.png" />

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_CreateUser7.png" />

そして```Create User```ボタンをクリックすると、ハイ出来上がり。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_CreateUser8.png" />

ちなみに右端のレンチのアイコンをクリックすると、ユーザ情報メンテ画面が表示されます。パスワード以外は大体操作できるようになっています。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_EditUser2.png" />

###ログイン処理を記述する

それでは先ほど作成したアカウント情報を使って、ログイン処理を記述してみましょう。

app.jsの先頭部にちょろっと追加してみました。

```JavaScript
(function() {
    // Loginのサンプル
    var Cloud = require('ti.cloud');    
    Cloud.Users.login({
        login:    'donayama',
        password: 'password'
    }, function (e) {
        if (e.success) {
            var user = e.users[0];
            alert('Success:\\n' +
                'id: ' + user.id + '\\n' +
                'first name: ' + user.first_name + '\\n' +
                'last name: ' + user.last_name);
        } else {
            alert('Error:\\n' +
                ((e.error && e.message) || JSON.stringify(e)));
        }
    });
    
	//determine platform and form factor and render approproate components
	var osname = Ti.Platform.osname,
		version = Ti.Platform.version,
		height = Ti.Platform.displayCaps.platformHeight,
		width = Ti.Platform.displayCaps.platformWidth;

	/* 略 */	
```

該当部分だけさらに抜き出してみます。

```JavaScript
    var Cloud = require('ti.cloud');    
    Cloud.Users.login({
        login:    'donayama',
        password: 'password'
    }, function (e) {
        if (e.success) {
            var user = e.users[0];
            alert('Success:\\n' +
                'id: ' + user.id + '\\n' +
                'first name: ' + user.first_name + '\\n' +
                'last name: ' + user.last_name);
        }
        /* elseは省略 */
    });
```

まず、```require('ti.cloud')```によって、ACSの操作を行うモジュールを呼び出します。
その直後に```Cloud.Users.login```というAPIをコールし、第一引数として、

```JavaScript
    {
        login:    'donayama',
        password: 'password'
    }
```

そして、第二引数として、

```JavaScript
    function (e) {
        if (e.success) {
            var user = e.users[0];
            alert('Success:\\n' +
                'id: ' + user.id + '\\n' +
                'first name: ' + user.first_name + '\\n' +
                'last name: ' + user.last_name);
        }
    }
```

を与えています。


第一引数は実行したいAPIに引き渡すパラメータとなり、ここでは```login```と```password```というパラメータ名に対する値として```'donayama'```と```'password'```を設定します。

また、サーバ側処理となるため必然的に非同期処理となり、第二引数として処理後のコールバック関数の設定をしています。
仮引数eにはサーバからのRESTレスポンス(JSON)をパースした状態で格納されており、e.successで認証可否のtrue/false値、e.users配列に認証されたユーザアカウントの情報が１つだけ設定されています。
これが配列で返送されるのはユーザ照会のCloud.Users.searchなどとレスポンス形式を同じにしているためです。
個々のuserオブジェクトには指定した氏名などが格納されています。

このソースで実行すると、次のような画面表示がされるはずです。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSTest1.png" />

###認証が不要な処理と必要な処理

このあと説明する写真の一覧表示と写真のアップロード処理なのですが、前者のような照会処理についてはユーザに紐づくような処理（たとえばTwitterでいうところのDM取得とか）を除けば```Cloud.Users.login```をしていなくてもAPIの呼出しは可能なのですが、後者のような投稿処理については「誰が投稿した」のかの紐付けが必要となるため、事前に```Cloud.Users.login```を通過しておかないと、エラーとなります。

そして認証ユーザセッションはアプリケーション内で維持されているのですが、（少なくとも現時点では）ログインしているか否かを判定するAPIが存在しないため、個別にログインユーザの情報を保持する必要がある点に注意する必要があります。

##写真の一覧表示

さて、ログイン処理を行ったので、次に写真の一覧表示をしたいと思います。

###ACSコンソールからの写真登録

といっても、現状登録されている写真データはログインアカウントに紐づけたアイコン画像のみ。
そこで手動でアップロードする手順も追いかけてみましょう。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData.png" />

ManageDataから```Show/Edit Photos```を選択すると、次のような一覧が表示されます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_UploadPhoto1.png" />

```Create a Photo```を選択すると、次のウィンドウが表示されます。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_UploadPhoto2.png" />

紐づけるユーザを選択して、ファイル選択ボタンで適当な画像を選択します。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_UploadPhoto3.png" />
<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_UploadPhoto4.png" />

```Create Photo```ボタンをクリックすると、一覧にサムネイルが追加されているのが分かりますね。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSAppsMenu_ManageData_UploadPhoto5.png" />

###写真一覧取得API

こうして追加された画像を表示するためのコードはどうなるのか見てみましょう。

```JavaScript
    var Cloud = require('ti.cloud');    
    Cloud.Photos.query({
        page: 1,
        per_page: 20
    }, function (e) {
        if (e.success) {
            for (var i = 0; i < e.photos.length; i++) {
                var photo = e.photos[i];
                // Create an ImageView.
                var anImageView = Ti.UI.createImageView({
                    image : photo.urls.thumb_100,
                    width : 100,
                    height : 100,
                    top :  100  * Math.ceil(i / 4),
                    left : 100 * (i % 4)
                });
                self.add(anImageView);
            }
        }
    });
```

このソースで実行すると、次のような画面表示がされるはずです。

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSTest2.png" />

さて、APIを分解してみますと```Cloud.Photo.query```の引数としてはログイン時と同様にパラメータ指定とコールバック関数をセットしています。

次のパラメータ引数はページネーションをするための指定で、1ページあたり20件表示の1ページ目という指定です
。
```JavaScript
    {
        page: 1,
        per_page: 20
    }
```

この指定条件として、位置情報を織り交ぜることも可能です。（どこなんだろう…）

```JavaScript
    {
         page: 1,
         per_page: 20,
         where: {
             coordinates: {
                 '$nearSphere': [-122.23,37.12],
                 '$maxDistance': 0.00126
             }
         }
    }
```

第二引数はコールバック関数で、仮引数e.photos配列に照会結果となるPhotoオブジェクトが要素として格納されます。
Photoオブジェクトのurlsプロパティにはサムネイル画像のURLを指すthumb_100という値が設定されており、これを利用してImageViewをせこせこと追加してみます。

```JavaScript
    function (e) {
        if (e.success) {
            for (var i = 0; i < e.photos.length; i++) {
                var photo = e.photos[i];
                // Create an ImageView.
                var anImageView = Ti.UI.createImageView({
                    image : photo.urls.thumb_100,
                    width : 100,
                    height : 100,
                    top :  100  * Math.ceil(i / 4),
                    left : 100 * (i % 4)
                });
                self.add(anImageView);
            }
        }
    }
```

###ACS APIリファレンス
Titanium MobileのAPIリファレンスとは別にACS自体のAPIリファレンスというものが用意されており、これを見る事で、APIのパラメータや仮引数eの中身について知る事が出来ます。

http://cloud.appcelerator.com/docs/

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSWebSite_APIRef1.png" />

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSWebSite_APIRef2.png" />

<img src="https://github.com/donayama/TiNote/raw/master/FirstStepGuide/images/ACSWebSite_APIRef3.png" />

##写真のアップロード

さて、写真のアップロードをしてみましょう。
カメラ撮影して即座にアップロードしても良いのですが、ここではフォトギャラリーからアップロード対象を選択する形式にしています。

```JavaScript
        Ti.Media.openPhotoGallery({
            success : function(event) {
                Cloud.Photos.create({
                    photo: event.media
                }, function (e) {
                    if (e.success) {
                        var photo = e.photos[0];
                        alert('Success:\\n' +
                            'id: ' + photo.id + '\\n' +
                            'filename: ' + photo.filename + '\\n' +
                            'size: ' + photo.size,
                            'updated_at: ' + photo.updated_at);
                    } else {
                        alert('Error:\\n' +
                            ((e.error && e.message) || JSON.stringify(e)));
                    }
                });
            },
            mediaTypes : [Ti.Media.MEDIA_TYPE_PHOTO]
        });
```

やっていることとしては```Cloud.Photos.create```に対して、フォトギャラリーで選択された写真```event.media```を設定して、送信しているだけです。
APIについてはこれまでと同様なので解説は割愛しますが、この時点でログインをしていないと、エラーになる点を注意したいところです。

以上で解説は一旦終了です。


