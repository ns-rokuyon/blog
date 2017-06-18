+++
date = "2017-06-12T18:54:29+09:00"
draft = false
slug = "unity-chronos"
categories = ["Unity"]
title = "Chronosを用いたポーズ処理の実装"

+++

## 概要

Chronos - Time ControlアセットをUnityのポーズ処理に使うやり方のメモ.

<!--more-->

Unityのポーズ処理はこれといって決まったやり方が無く,地味に面倒.  
調べると一番出てくるやり方はTime.timeScaleを0にする方法だが,
よほどシンプルなゲームでない限り,うまくいかない.  
ポーズ中に一部のオブジェクトはポーズを無視させたいような状況が発生すると工夫が必要となる.

一般的にTime.timeScale=0以外だと[Unity4/C# ポーズ処理の実装をどうするか考えた話 - Qiita](http://qiita.com/24kwakahana/items/cee31950303f7dfb23bc)
に挙げられているような方法がよく使われている模様.


## Chronosアセットについて

[![](http://img.youtube.com/vi/-zq1OZuGXoc/0.jpg)](https://www.youtube.com/watch?v=-zq1OZuGXoc)

[Chronos - Time Control - アセットストア](https://www.assetstore.unity3d.com/jp/#!/content/31225)

UnityでのTime controlをやりやすくするアセット.
動画で紹介されている機能は

- SLOW TIME
- PAUSE TIME
- REWIND TIME
- GROUP CONTROL
- OBJECT CONTROL
- AREA CONTROL
- PHYSICS CONTROL
- PHYSICS REWIND

ポーズ処理のみに使うにはオーバースペック感がすごい.


## 使い方

### 基本

{{< figure src="/images/20170612/1.png" title="" class="half-size" >}}

Timekeeperコンポーネントを付けたTimekeeperオブジェクト(シングルトン)をシーンに追加する.
時間を操作したいグループ単位でGlobalClockコンポーネントを増やす.

GlobalClockは階層構造を持てる.  
Parentで指定したGlobalClockが親になり,Timescaleの変化などが子に伝播する(どう扱うかをParent Blendで設定できる)


{{< figure src="/images/20170612/3.png" title="" class="half-size" >}}

Clockの管理下にしたいオブジェクトにはTimelineコンポーネントをつける.  
GlobalClockでTimekeeperに付けてあるGlobalClockの中のどれかを選べるようになっている.

Rewindableにチェックを入れるとRewindができるようになるが,多少メモリを余分に消費するらしい.
ポーズ処理のみに使うのであればチェックを入れない方が良いかも.


timeScaleの設定

~~~cs
// Chronosを使わない場合
Time.timeScale = 0.0f;

// Chronosを使う場合
// keyはGlobalClockの名前
Timekeeper.instance.Clock(key).localTimeScale = 0.0f;
~~~


各オブジェクトからの時間情報の取得する際は,
Timelineコンポーネントを介して取得する.

~~~cs
var timeline = GetComponent<Timeline>();

// timeScaleを取得
bool paused = timeline.timeScale == 0.0f;

// deltaTimeの取得
float dt = timeline.deltaTime;
~~~

### Rigidbody

RigidbodyをChronosの管理下にしたい場合は,Timelineコンポーネントが持つRigidbodyTimeline3DクラスをRigidbodyの代わりに使う.
当然,Rigidbodyコンポーネントも付けておく必要あり.

~~~cs
var timeline = GetComponent<Timeline>();

// 速度の取得
Vector3 v = timeline.rigidbody.velocity;

// 力を加える
timeline.rigidbody.AddForce(f);
~~~


## ポーズ処理

例えば,動きを止めたいグループのClockのみtimeScaleを0にするなど.

~~~cs
public static void pause() {
    clockNamesToPause.ForEach(key => {
        Timekeeper.instance.Clock(key).localTimeScale = 0.0f;
    });
}
~~~


## 参考ページ

- [Chronos - Time Control - アセットストア](https://www.assetstore.unity3d.com/jp/#!/content/31225)
- [Chronos - Time Control for Unity - YouTube](https://www.youtube.com/watch?v=-zq1OZuGXoc)
- [Unityで時間制御ができるアセット「Chronos - Time Control」を試してみました - Cross Road](http://magicbullet.hatenablog.jp/entry/UnityAsset_Chronos)
- [Chronosで時間の制御【UnityでRTSを作る 9】 - ヒキニートがゲームを作るブログ](http://mackysoft.hatenablog.com/entry/2017/05/03/210000)
- [Unity4/C# ポーズ処理の実装をどうするか考えた話 - Qiita](http://qiita.com/24kwakahana/items/cee31950303f7dfb23bc)
