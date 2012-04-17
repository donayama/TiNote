第二章 Titanium Mobile環境構築（１）開発環境の準備
========================================================
Titanium Mobileでの開発に必要となる統合開発環境Titanium Studioをはじめ、iOS・Androidの各開発環境の導入を行い、Hello Worldアプリケーションを開発する準備をしましょう。

##準備の前に
Titanium Mobileの動作には以下の環境が必要となりますので、まずはやりたいことが出来る環境をお持ちかどうかご確認ください。

###OSとプラットフォームの対応
<table border="2">
	<tr>
		<th>利用環境</th>
		<th>iOS (4.0~)</th>
		<th>Android (2.2~)</th>
		<th>BrackBerry(α)</th>
	</tr>
	<tr>
		<th>Mac OS X<br />(Snow Leopard,Lion)</th>
		<td>○</td><td>○</td><td>×</td>
	</tr>
	<tr>
		<th>Windows<br />(Windows7, XP(SP3+))<br />※Vista非対応</th>
		<td>×</td><td>○</td><td>○</td>
	</tr>
	<tr>
		<th>Ubuntu<br />(10.04+)</th>
		<td>×</td><td>○</td><td>×</td>
	</tr>
</table>

###JDKの対応バージョン
<table border="2">
<tr>
<th>利用環境</th>
<th>対応バージョン</th>
<th>アーキテクチャ</th>
<th>備考</th>
</tr>
<tr>
<th>Mac OS X</th>
<td>1.6(rev 10~最新)</td>
<td>32bits/64bits</td>
<td>-</td>
</tr>
<tr>
<th>Windows</th>
<td>1.6(rev 10~最新)</td>
<td>32bits</td>
<td>-</td>
</tr>
<tr>
<th>Ubuntu</th>
<td>1.6(rev 10~最新)</td>
<td>32bits/64bits</td>
<td>64bit の場合は ia32-libs が必要</td>
</tr>
</table>

またTitanium Studioの動作に1GBのメモリ、Android SDKの動作にそれに加えて1~1.5GBのメモリが必要となります。
Titanium Mobileでの開発には2~3GBのメモリを確保する事が推奨されています。

###iOSにはOSX環境が必要

iPhone・iPad・iPod touchなどのiOSデバイス向け開発にはiOS SDKとXcodeが必要となり、システム要件もそれに連動します。開発機のOSとしてMac OSXが必要です。
そのため、Windows・UbuntuではiOS開発はできません。

また、iOS SDKは常に最新のMac OSXのバージョンを要求する傾向が強くあり、最新版である SDK 5.1.0ではOSX 10.6(Snow Leopard)が最低条件となっています。
今後のiOS SDKでは 10.7(Lion)以上が要件となってくるでしょう。

###Android開発について

Androidはサポートされている環境で開発でき、Android 2.2以降をサポートしています。
(Titanium Mobile 1.8.0よりiOS SDK 3.2.2以前、Android SDK 2.1以前がサポートされなくなりました。)

##iOS用開発環境の構築

###iOS SDKとXcodeのインストール

Titanium Mobileの使用の有無に関わらず、iOS用アプリケーションの開発にはXcodeならびにiOS SDKをインストールしておく必要があります。
これらのソフトウェアは以前 iOS Dev Centerへのユーザ登録の上、ダウンロードする必要がありましたが、現在はその他にもMac App Storeを経由してダウンロードすることも可能になっています。

このインストーラのファイルサイズは4GB以上という巨大なファイルなっているため、時間とネットワーク帯域に余裕のあるときにダウンロードしておきましょう。さらに初期インストール時には10GB弱のディスク容量を必要としますので、残容量に不安がある場合はディスク内をあらかじめ整理した上で、この作業に入りましょう。

- iOS Dev Center
	- http://developer.apple.com/devcenter/ios/
- Mac App Store Xcode
	- http://itunes.apple.com/jp/app/id448457090

Mac App Storeからダウンロードしたファイル(アイコンのタイトルはInstall Xcode)をダブルクリックして実行します。

原則的にインストーラの指示に従い、「次へ」と進めていくだけで結構ですが、環境によってはインストール途中で```In order to continue installation, please close the following application: iTunes```のメッセージが表示されますの で、その際にはアクティビティモニタを起動し ```iTunesHelper```を選んで```終了```するようにしてください。

また地味に気付きづらい注意点なのですが、**XcodeのアップデートをMac App Storeからした場合、ダウンロードだけをしても更新されません**。LaunchPadからInstall Xcodeを再び実行する必要があります。

###コラム:Xcode-selectによるXcodeの複数バージョン併用
SDKのベータバージョンを入れるなど、複数のXcodeを同一環境に入れる必要がある場合、切替に```xcode-select```コマンドを使うことでデフォルトのXcodeを切り替えることができます。このデフォルトのXcodeがTitanium Mobileで使用されるものとなります。

    #（/Developerは戻したいSDKの入っているディレクトリ）
    /usr/bin/xcode-select -switch /Developer

###iOS Developer Programの登録

iOSデバイス向けの開発を本格的に進めていくためにはiOS Developer Programという有償サービスに参加する必要があります。
登録には年間8800円（2012年1月現在）が必要となります。

このサービスに登録しない限り、iOSシミュレータ上での動作検証しか行えません。カメラや加速度センサ・電子コンパスといったデバイスに依存する機能については検証を行うことができませんし、たとえ無料アプリ公開だけが目的であってもAppStoreへの登録も一切出来ません。

すぐさま必要になるものではありませんので、必要になってから登録を行いましょう。

####はみだしコメント

> Mac App Store経由の場合、バージョンアップ時の差分インストールができるため、初回インストール以降の時間短縮が図れます。
> 一方で、リリース版のXcodeならびにiOS SDKのみの利用となる弊害もあります。ただし後述する理由からベータ版のiOS SDKインストールがTitanium Mobileでは推奨されないため、筆者としてはOSがLionである場合は、Mac App Store経由でのダウンロードをおススメしています。


####コラム:実はオススメできないベータ版iOS SDKインストール

> Titanium Mobileで実際の実行ファイルを生成するためにiOS SDKを使用することは前述しましたが、それはビルド時に用いるファイルがiOS SDKそのものであることも指しています。
> メーカであるAppceleratorは正式公開版のiOS SDK(ならびにXcode)については公開後、速やかな対応をする動きをしておりますが、開発者向けのベータ公開版については原則的に(無償)サポート対象外となっています。
> ベータ版SDKを一度開発環境にインストールしてしまうと、```Xcode-select```コマンドのお世話になるハメになり、何かと面倒です。
> 本当にこの環境にベータ版SDKをインストールする必要があるのかについて、よくよく考えてからインストールしてみましょう。


##Android SDKの導入

Android開発を行うためにはシステムにJava SDK（JDK）が導入されている必要があります。Titanium Mobileにおいて現在サポートされているのはJDK 6（1.6）のみです。
すでにJava開発環境を導入しているシステムでは```java -version```コマンドや```javac -version```コマンドの実行結果として```1.6.x```と表示されれば、そのままAndroid SDKの導入のステップに移ることができます。


    $ java -version
    java version "1.6.0_22"
    Java(TM) SE Runtime Environment (build 1.6.0_22-b04-307-10M3261)
    Java HotSpot(TM) 64-Bit Server VM (build 17.1-b03-307, mixed mode)
    $ javac -version

javac 1.6.0_22環境によってはパスが通っていない状態などがありますので、環境変数を適切に設定して認識するようにしてください。

###Android SDKのダウンロード
Android Developerサイト(http://developer.android.com/sdk/index.html)からAndroid SDKをダウンロードします。
各プラットフォームに適したファイルをダウンロードしてください。

###Mac OSへの導入
Mac OS X 10.6(Snow Leopard)以前であれば、JDKがすでにインストールされていましたが、Mac OS X 10.7(Lion)以降は標準でインストールされていません。

Mac OS X 10.7を利用されている場合はアプリケーションフォルダ配下のユーティリティフォルダ内にある「Java Preference」を実行し、画面の指示に従って、Java for Mac OS X 10.7をダウンロード・インストールをしておいてください。

ダウンロードしておいたAndroid SDKのzipファイルを展開し、アプリケーションフォルダにファイルをすべて移動します。
移動した```android-sdk-mac_x86```フォルダの```tools```フォルダ内にある```android```コマンドを実行してください。

Available packages から以下のPackageにチェックを付け、ダウンロードして Installed Packages に表示させていきます。（Android 3.x系が必要であれば追加してください）

- Android Repository
	- Android SDK Tools, revision 15以上
	- Android SDK Platform-tools, revision 9以上
	- SDK Platform Android 2.3.3, API 10
		- SDK Platform
		- Google APIs
	- SDK Platform Android 2.2, API 8
		- SDK Platform
		- Google APIs

確認画面では```Accept All```を選択後、```Install```ボタンをクリックします。
￼続いてターミナルを起動し、以下のコマンドを実行し、Android SDK 内にある adb のシンボリックリンクを作っておきます。
インストール先のフォルダを変更している場合は、適宜読み替えてください。

    $ ln -s /Applications/android-sdk-mac_x86/platform-tools/adb /Applications/android-sdk-mac_x86/tools

###Windows7への導入
Windows7には標準でJava実行環境がインストールされていませんので、まずはJDKのセットアップを行います。Windows XPをお使いの場合もJava6がTitanium MobileにおけるAndroid開発前提になりますので、インストールされていない場合は同じ手順が必要となります。

Java SEダウンロードページにアクセスし、JDKダウンロードボタンをクリックします。お使いの環境に合わせてWindowsを選択し、続く画面からセットアップファイルをダウンロード後、実行してください。
(お使いのOSが64bitsでも対応しているJDKは32bits版のみとなります)

インストーラの指示に従って、インストールした後、コマンドプロンプトを起動して```java -version ```と ```javac -version```を実行し、動作確認を行います。

    Microsoft Windows [Version 6.1.7600]
    Copyright (c) 2009 Microsoft Corporation.  All rights reserved.
    
    C:\Users\UserName>java -version
    java version "1.6.0_23"
    Java(TM) SE Runtime Environment (build 1.6.0_23-b05)
    Java HotSpot(TM) Client VM (build 19.0-b09, mixed mode, sharing)
    
    C:\Users\UserName>javac -version
    'javac' は、内部コマンドまたは外部コマンド、
    操作可能なプログラムまたはバッチ ファイルとして認識されていません。
    C:\Users\UserName>

```javac```コマンドにパスが通っていないとTitanium Mobileが正しく動作しないため、環境変数に設定を追加します。システムのプロパティを開き「システムの詳細設定」を選択します。
￼
詳細設定タブの「環境変数」を選択して、システム環境変数のPATHの末尾にJavaSDKのインストール先のbinフォルダを半角のセミコロン（；）を付けて追記してから、もう一度バージョン確認処理を実行してください。
（例では、```〜;C:¥Progaram Files¥Java¥jdk1.6.0_23¥bin```）
続いてAndroid SDKをインストールします。
ダウンロードしたセットアップファイルを実行し、インストーラの指示に従って進めてください。完了後、SDK Managerを起動し、APIパッケージをインストールします。インストールするパッケージについてはMaxOSXと同様になります。
```adb```のシンボリックリンクについてはコマンドプロンプトを管理者権限で立ち上げ、次のコマンドを実行する必要があります。

    cd C:\Program Files\Android\android-sdk-windows\tools
    mklink adb.exe ..\platform-tools\adb.exe
    mklink AdbWinApi.dll ..\platform-tools\AdbWinApi.dll

Windows XPなどでシンボリックリンクを作りづらい状況では、mklinkコマンドを実行する代わりに```adb.exe```と```AdbWinApi.dll```を```platform-tools```フォルダにコピーすることでも対処可能です。
