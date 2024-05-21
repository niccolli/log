---
layout: default
title:  "Raspberry PiでSPIディスプレイに画面を表示させる (64bit対応)"
date:   2024-05-22 08:00:00 +0900
categories: make raspberrypi
---

# Raspberry PiでSPIディスプレイに画面を表示させる (64bit対応)

[過去の記事](https://niccol.li/log/make/raspberrypi/2022/07/02/fbcp_ili9341_rpi4.html)で、Raspberry PiでSPIディスプレイを使うにはfbcp-ili9341を使って表示させる方法を記載しましたが、残念ながらfbcp-ili9341は64bitに対応できない・参照しているライブラリが古いとの理由で[開発終了](https://github.com/juj/fbcp-ili9341)となりました。

一方で、Raspberry PiでSPIディスプレイを扱うのにあたって新しいドライバがLinuxカーネルに組み込まれるようになりました。mipi-dbi-spiです。この記事では、64bit版Raspberry Pi OSでの適用方法を記載します。

## 設定方法

mipi-dbi-spi経由のSPIディスプレイ制御を行うにあたり、次の対応が必要になっています。

1. /boot/firmware/config.txt に設定項目を記載
1. "ファームウェア"をビルドして /lib/firmware/panel.bin として保存

これらの対応で、Raspberry Pi起動後にデスクトップをそのままSPIディスプレイに表示できるようになります。

### config.txt に設定項目を記載

/boot/firmware/config.txt の末尾に、次のように記載します。

```
dtoverlay=mipi-dbi-spi,spi0-0,speed=70000000
dtparam=width=320,height=240
dtparam=reset-gpio=24,dc-gpio=22
dtparam=backlight-gpio=23
```

GPIO(Reset/DC/Backlight)は、自身の接続先に合わせて調整してください。

ILI9341の場合、[データシート](https://cdn-shop.adafruit.com/datasheets/ILI9341.pdf)を読むと100nsが最小(18.3.4 Display Serial Interface Timing Characteristics (4-line SPI system)のtwcの値)なので、最速は10MHzとなりますが、真面目に10MHzに設定するとカクカクして扱いづらいです。実力値で70MHzを設定しているようです。

### "ファームウェア"をビルドして配置

これが一番肝心なようです。Raspberry Pi環境で実行する場合、mipi-dbi-spiドライバは特に指定がない場合は/lib/firmware/panel.binをファームウェアとしてSPIディスプレイに適用します。これは下記の手順にてビルドができます。

1. https://github.com/notro/panel-mipi-dbi から mipi-dbi-cmd をダウンロード。なかみはPythonのスクリプトです。
1. https://github.com/notro/panel-mipi-dbi/wiki/Displays から、使用するディスプレイのコントローラーに合わせてテキストファイルを作成する。
   - ILI9341の場合は[こちら](https://github.com/notro/panel-mipi-dbi/wiki/Displays#adafruit-ili9341-pitft-tft-lcd-screen)
1. python mipi-dbi-cmd panel.bin ili9341.txt と実行してファームウェアをビルド
1. panel.bin を /lib/firmware にコピー
1. Raspberry Piを再起動

うまく設定できた場合、dmesgに次のように記録されます。フレームバッファが設定され、ディスプレイにデスクトップ画面が表示されます。

```
[   10.320278] [drm] Initialized panel-mipi-dbi 1.0.0 20220103 for spi0.0 on minor 2
[   10.949283] panel-mipi-dbi-spi spi0.0: [drm] fb0: panel-mipi-dbid frame buffer device
```

![](https://raw.githubusercontent.com/niccolli/log/main/images/20240522.jpg)

## 活用例

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">SPIのディスプレイでかなりスムーズに表示できるぞ <a href="https://t.co/5UZmNeDWhw">pic.twitter.com/5UZmNeDWhw</a></p>&mdash; niccolli (@niccolli) <a href="https://twitter.com/niccolli/status/1775885281532784962?ref_src=twsrc%5Etfw">April 4, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

もたつくようなこともなくスムーズに表示されています。じっと見つめていると反映されていない箇所もあるように見えますが、動画の場合はすぐ書き換えられるのであまり気にならないと言えるでしょう。
