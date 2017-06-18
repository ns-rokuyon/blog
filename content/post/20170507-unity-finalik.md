+++
date = "2017-05-07T18:58:27+09:00"
draft = false
slug = "unity-finalik-weapon"
categories = ["Unity"]
title = "Final IKで武器を拾って使う"

+++

## 概要

Final IKアセットを利用して,ステージに落ちている武器を拾って使えるようにするための手順のメモ.

<!--more-->

キャラクタにはユニティちゃんを使う.


## コンポーネント

{{< figure src="/images/20170507/1.png" title="ユニティちゃん" class="half-size" >}}

ユニティちゃんにはFinal IKとFull Body Biped IKとInteraction Systemをアタッチする.
Full Body Biped IKを事前にセットしておけば,Interaction Systemの必要項目は自動的に設定される.


{{< figure src="/images/20170507/6.png" title="Character1_RightHand" class="half-size" >}}

ユニティちゃんの武器を握ることになるオブジェクト(今回は右手)にFinal IKのHand Poserコンポーネントをアタッチ.


{{< figure src="/images/20170507/3.png" title="ShortSword > Character1_RightHand" class="half-size" >}}

武器に合わせる手のターゲットとなるオブジェクトにInteraction Targetコンポーネントをアタッチ.
手のターゲットの作り方は後述.

Effector TypeはRight Handを指定.


{{< figure src="/images/20170507/5.png" title="ShortSword" class="half-size" >}}

武器オブジェクトにInteraction Objectコンポーネントをアタッチ.  
[FINAL IK TUTORIALS](https://www.youtube.com/playlist?list=PLVxSIA1OaTOu8Nos3CalXbJ2DrKnntMv6)に従って,Weight Curves,Multipliers,Eventsを設定する.  

Weight Curvesはインタラクション時に手のポジションをどのように近づけるかをAnimation Curveで指定できる.  
EventsのPick Upにチェックを入れると,インタラクション時の指定したTime(Weight Curvesの横軸に対応)に
インタラクション先となるオブジェクト(武器)をインタラクション元となるオブジェクト(右手)の子オブジェクトにしてくれる(便利!).

動画にも説明がある通り,Interaction ObjectにCollider(Box Colliderなど)がアタッチされていると挙動がおかしくなる.  
拾う前の武器オブジェクトを床に置いたり,落下させたいことがある場合は,武器オブジェクトにRigidbodyをつけ,
武器オブジェクトの子オブジェクトにColliderをつけたCubeなどを入れておくことで対応できる.
武器オブジェクトにRigidbodyをつけていても問題なく拾うことができるが,拾って手の子オブジェクトになった後は,
Final IKが自動的にRigidbodyのIs KinematicをTrueにしてくれる.


## インタラクション先に右手オブジェクトを作る

{{< figure src="/images/20170507/2.png" title="Interaction target" class="half-size" >}}

チュートリアル動画が分かりやすいので,見ながらやる.

シーン上に一時的にコピーしたユニティちゃんモデルの右手を武器の持ち手部分に移動させて,
指の曲がり方などを右手の関節となる子オブジェクトの位置と角度を調節していく.
結構めんどくさい作業.


## Hand Poserの設定

{{< figure src="/images/20170507/8.png" title="武器を拾ったユニティちゃん" class="half-size" >}}

{{< figure src="/images/20170507/9.png" title="握れていない" class="half-size" >}}

Pick Upした後の右手がインタラクション先に設定した右手座標の通りになっていない.


{{< figure src="/images/20170507/10.png" title="Hand PoserのWeightを1.0に" class="half-size" >}}

Hand PoserのWeightを1にするとインタラクション先のポジションと一致する.


## 参考ページ

- [FINAL IK TUTORIALS](https://www.youtube.com/playlist?list=PLVxSIA1OaTOu8Nos3CalXbJ2DrKnntMv6)
- [徒然なるままに Final IKについて（その１）](http://miyas-maincontents.blogspot.jp/2016/11/final-ik.html)

