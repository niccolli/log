---
layout: default
title:  "Raspberry Piをいい雰囲気の端末にする基板"
date:   2022-07-24 22:30:00 +0900
categories: make raspberrypi
---

# Raspberry Piをいい雰囲気の端末にする基板

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_2.jpg)

という物を作りました。良い物を作ったと大変気に入っているので、アウトプットを残しておきます。

## なぜ作ったか

### Raspberry Piを持て余していた

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_1.jpg)

Raspberry Pi 4を1台、常時動かしており、かつてはAirPlayサーバーとTime Machineサーバー(と時計)として使っていました。Macを持ち歩くことが多かったのでTime Machineも切り離していましたが、Macを持って外出することがほとんどなくなったため、Time Machine用ハードディスクはMacにつなぎっぱなしになりました。しかもそのほうが速かった。そのため、ここ最近のRaspberry Pi 4は基本的にAirPlayサーバーとしてのみ動いていました。ちょっとその性能からはもったいない形です。

せっかくなので、SPI経由でディスプレイを映せないか、数年前はfbtftなどあったけど今はどうなんだろうと調べてみてうまくいったのは[既報](https://niccol.li/log/make/raspberrypi/2022/07/02/fbcp_ili9341_rpi4.html)のとおりです。わりとスムーズに動くことがわかって活用ができそうと思うも、ジャンパ線を都度間違えないようにつなぐのは手間でストレスです。なので基板に落とし込むこととしました。

適度なサイズのディスプレイがスムーズに動くので、HomeKitのコントローラーや、お天気などの自分用サイネージとしても使えそうと思い、ちょっといい外見を整えることを考えました。

## なぜこの形になったか

### 天板と構造

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_3.jpg)

詰めたサイズにするならば[市販品](https://www.switch-science.com/catalog/2449/)を買えばいいので、自分で作る必要はないと思います。ちょうど[KORG NTS-2の発売のお知らせ](https://www.korg.com/jp/products/dj/nts_2_pt/)を見て、このインタフェースとサイズ感がいいなと思い、近しい配置・構造にしました。Raspberry Piの上に基板を広げることで、基板が箱の役割も兼ねています。

ディスプレイ横に7セグメントLEDの時計を、ディスプレイ下にスイッチを置いています。スイッチは余ったGPIOにつないでいて、追ってHomeKitやNatureRemoを動かすコントローラーなどにも使うことを考えています。キースイッチにして、用途に合わせてかわいいキートップに置き換えるのもいいかなと思っています。

### 底板

基板発注は10枚まで値段が変わらないことが多いので、必然的に余ります。余った基板は上下に重ねることでネジ位置が合うため、組み合わせると天板・底板として活用できます。

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_4.jpg)

ディスプレイ配置穴は、底板に使うとケーブル類の取り回しに活用できました。これは設計時は考えていなかった偶然です。

### ディスプレイ組み付け

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_5.jpg)

[秋月電子で購入できるモジュール基板](https://akizukidenshi.com/catalog/g/gM-16265/)を、裏から組み付けています。赤い基板は要らないので本体だけの購入ができないか探しましたが入手性のよさそうなものはなく、秋月電子の販売価格も穏やかなのでそのまま使うこととしました。

このモジュール基板は、ディスプレイ側のピンヘッダがあらかじめハンダ付けされています。そのまま組み付けると赤い基板が目立つので、一旦ピンヘッダを全部外し、天板裏面から細ピンヘッダで位置を調整しながらハンダ付けしています。これも設計時は考えていなかった偶然ですが、この構造にしたことでディスプレイ表面と天板がツライチで揃う形になりました。

## 感想とこれから

### 見た目がいい

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_6.jpg)

見た目がよくとても気に入っています。机上に置いておくのもいいですが、写真のようにシンセサイザーのスペースにぴったり嵌まってしまい、楽器と合わせた機器として活用するのもよいのではと考えています。

### ケーブル抜き差し時の不安解消

Raspberry Pi本体はピンヘッダでのみ固定されているので、電源等のケーブル抜き差しで不安定になります。ネジ穴で固定できるようにするため、底面板を板金加工で作り直しています。

### 頒布するかどうか

「数を作って頒布してほしい」という話を聞いています。ありがたい限りですが、先のディスプレイ組み付けがあまり他人にお勧めできないため、ちょっと悩ましいところです。LEDは[秋月電子](https://akizukidenshi.com/catalog/goods/search.aspx?search=x&keyword=OSL40391&image=%8C%9F%8D%F5)に、スイッチは[千石電商](https://www.sengoku.co.jp/mod/sgk_cart/search.php?multi=B3J)にそれぞれカラーバリエーションがあるので、好きな色を選んでもらうのは良いかなと思っています。

### ソフトウェアを作り込んでいく

![](https://raw.githubusercontent.com/niccolli/log/main/images/20220724_7.jpg)

下回りの開発・改善は一段落と思うので、ディスプレイを活用した見栄えのいいインタフェースを作っていこうと思います。AirPlayサーバーの[shairport-sync](https://github.com/mikebrady/shairport-sync)では今流している曲の情報を受け取れるので、まずはそれらを表示する仕組みを整えようと思います。使い方のわからない抵抗被膜タッチインタフェースも手をつけないとと思いますが、スイッチがついているのであまり使わないかなとも思っています。