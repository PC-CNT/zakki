<!-- omit in toc -->
# Xposed_module.apk

- [注意](#注意)
- [環境構築](#環境構築)
  - [LSPosedの導入](#lsposedの導入)
- [Xposed moduleの作成](#xposed-moduleの作成)
  - [プロジェクトの作成](#プロジェクトの作成)
  - [Xposedで使うための設定](#xposedで使うための設定)
    - [依存関係の追加](#依存関係の追加)
    - [メタデータの追加](#メタデータの追加)
- [処理の記述](#処理の記述)
  - [main.kt](#mainkt)
  - [構文とか](#構文とか)
  - [xposed_init](#xposed_init)
- [蛇足](#蛇足)
- [参考](#参考)

## 注意

私は泥に関する知識がまったくないです

多分何かしら間違ってます　ゆるして

## 環境構築

必要なもの

- [Android Studio](https://developer.android.com/studio)
- Root化済みのAndroid端末

~~泥側の環境構築に関しては割愛します~~

### LSPosedの導入

root化は端末によってやり方が異なるので端折ります

Zygisk（Magisk v24以降）

- [Releases · topjohnwu/Magisk](https://github.com/topjohnwu/Magisk/releases)

Magisk Managerの設定からZigiskを有効にする

LSPosed

- [Releases · LSPosed/LSPosed](https://github.com/LSPosed/LSPosed/releases)
- [LSPosedArchives（Telegram）](https://t.me/LSPosedArchives)

zygisk-release.zipを落としてMagiskからインスコ

## Xposed moduleの作成

### プロジェクトの作成

ファイル -> 新規 -> 新規プロジェクトから空のプロジェクトを選択、作成

### Xposedで使うための設定

#### 依存関係の追加

`settings.gradle` を開く

```gradle
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url "https://api.xposed.info/" }
    }
}
```

↑ `maven { url "https://api.xposed.info/" }` を追加する

___

次に `app/build.gradle` を開く

```gradle
dependencies {
    compileOnly  'de.robv.android.xposed:api:82'
    compileOnly  'de.robv.android.xposed:api:82:sources'
}
```

↑の文を追加する

___

#### メタデータの追加

`app/src/main/AndroidManifest.xml` を開く

```xml
<application>

    <meta-data
        android:name="xposedmodule"
        android:value="true" />
    <meta-data
        android:name="xposeddescription"
        android:value="ここに説明を入力" />
    <meta-data
        android:name="xposedminversion"
        android:value="82" />
    <meta-data
        android:name="xposedscope"
        android:value="android;com.android.chrome" />

</application>  
```

↑の文を追加（各値の有無や内容は場合によって変わる）

___

`xposedmodule`  
おそらく必須

`xposeddescription`  
モジュールの説明　下画像参照

`xposedminversion`  
~~基本は82でいいっぽい？と思ってたら93が最新らしい~~  
[New XSharedPreferences](https://github.com/LSPosed/LSPosed/wiki/New-XSharedPreferences)で使うとのこと

`xposedscope`  
モジュールを有効にしたときLSPosed側で自動的に選択されるスコープ  
"Recommended" のやつ  
[android:resource](https://developer.android.com/guide/topics/resources/providing-resources)でも指定できる

___

ビルドできたら多分成功

この時点でLSPosed側からモジュールとして認識されるはす（有効化はできない）

![スクショ](../images/Screenshot_01.jpg)

ここからモジュール本体のコードを書いていく（予定）

## 処理の記述

### main.kt

プロジェクトのソースディレクトリ（`app/src/main/java/……/`）に適当な名前のファイルを作成

今回は `main.kt` にした

```kotlin
package com.example.module_test;

import de.robv.android.xposed.IXposedHookLoadPackage
import de.robv.android.xposed.XposedBridge
import de.robv.android.xposed.callbacks.XC_LoadPackage
import de.robv.android.xposed.XSharedPreferences
import de.robv.android.xposed.IXposedHookZygoteInit

class main : IXposedHookZygoteInit, IXposedHookLoadPackage {
    // ここに処理を書く
    @Throws(Throwable::class)
    override fun initZygote(startupParam: IXposedHookZygoteInit.StartupParam) {
    }
    @Throws(Throwable::class)
    override fun handleLoadPackage(lparam: XC_LoadPackage.LoadPackageParam) {
    }
}
```

中身は場合によって変わる

### 構文とか

追記修正中……

### xposed_init

`app/src/main/assets/` に `xposed_init` という名前のファイルを作成する

```text
com.example.module_test.main
```

ここに書いたクラスが最初に読み込まれる

## 蛇足

How to make Xposed module　で検索するといくつか記事が出てくるけど

どの記事も`settings.gradle`の記述が

```gradle
repositories {
    jcenter()
}

dependencies {
    provided 'de.robv.android.xposed:api:82'
    provided 'de.robv.android.xposed:api:82:sources'
}
```

こんな感じになってる

実際にAndroid Studioで書いてみると

>JCenter Maven repository is no longer receiving updates: newer library versions may be available elsewhere

こんな警告がでる

調べてみるとJCenterなるライブラリのレポジトリを担うサービスが終了したとのこと

（つまりJCenterはPyPIとかnpmとかの認識でいいのかな……？）

一応読み取り専用で使えているらしいけどいつまで使えるかも不確定なので極力使わない方が無難

dependenciesの行では

>provided is deprecated; replace with compileOnly

と言われる

「providedは非推奨の書き方だからcompileOnlyに変えてね」とのこと

## 参考

[XposedBridge Wiki](https://github.com/rovo89/XposedBridge/wiki/Development-tutorial)

[LSPosed Wiki](https://github.com/LSPosed/LSPosed/wiki)

[Xposed Framework API](https://api.xposed.info/reference/packages.html)
