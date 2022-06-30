---
layout: default
title:  "Raspberry PiでGPIOをキーボードがわりにする"
date:   2022-06-30 23:30:00 +0900
categories: make raspberrypi
---

# Raspberry PiでGPIOをキーボードがわりにする

Rsapberry PiのGPIOの先にスイッチをつけたとき、DeviceTreeを適切に設定することでキーボード操作と同様のアクションを実装することが可能です。

## 設定

[/boot/overlays/README](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README)のgpio-keyを有効にします。/boot/config.txtに、次のように設定を加えます。

```
dtoverlay=gpio-key,gpio=6,active_low=1,gpio_pull=off,label=SW2,keycode=57
```

- gpio: スイッチを接続したピン番号を指定。
- active_low: 1＝Active Lowを有効。0＝無効。信号線がスイッチを押してGNDに落ちるならば1です。
- gpio_pull: ちょっとよくわかっていません。内蔵プルアップ・プルダウンの設定なのかと思いますが、初期値のGPIO3はupに設定されているも、外部にもプルアップ抵抗はありますと書いてあります。外部プルアップ抵抗がある場合、offでも動きました。
- label: /proc/device-tree/button@[ピン番号]/key/label に反映されます。動作には直接影響ありません。
- keycode: スイッチが押されたときに出力されます。[input-event-codes.h](https://github.com/torvalds/linux/blob/v5.15/include/uapi/linux/input-event-codes.h)に記載の数字を指定します。

## 注意など

### 正しいKeycodeを指定しなかった場合

keycodeでなんか希望の文字でも書けばいいのかなとアルファベットなどを指定した場合、116にフォールバックされるようです。116は上記ヘッダファイルで KEY_POWER と定義されているため、Raspberry Piの電源が落ちます。

このとき、「GPIO3をLowに落としてWake up」は有効になっている電源状態なので、GPIO3にもスイッチなど設定していれば復帰できます。

### チャタリング防止フィルタの有無

あったほうがいいのかもしれないですが、なしでも何度も反応している様子はありません。Linux側がうまく吸収しているのでしょうか。この辺りはまだ調べきれていません。

### キー操作をトリガーにしてなんらかのアプリケーションなどを動かす

Node.jsやPythonなどで監視して対応する例は見つかりますが、OSだけでなんとかする例はまだわかっていません。きっとあると思うんですけどね。

### Linuxのgpio-keysとは別物

別物というか、継承しつつRaspberry Piに合わせて調整しているようです。名前も微妙に違います。
