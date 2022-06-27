---
layout: default
title:  "Raspberry Pi OS BullseyeでのPWMの設定方法"
date:   2022-06-27 22:30:00 +0900
categories: make
---

# Raspberry Pi OS BullseyeでのPWMの設定方法

だいたい[こちらの記事](https://heavymoon.org/2021/09/11/raspberrypi-pwm-mx1508/)に書いてあった。ありがたい。

pwmを筆頭に、過去のさまざまなドライバの実装などがDeviceTreeに集約されているらしく、[/boot/overlays/README](https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README)を参照すると結構な数の登録がなされている。[fbtft](https://github.com/raspberrypi/firmware/blob/cbcefbc87a72458406c924f44d2525fe76470efb/boot/overlays/README#L912)はこちらに移って生き残っていることがわかる。

## 課題

pwmの場合、/boot/config.txtに記載すると起動後に/sys/class/pwm以下に制御ファイル群が作成され、Linuxのしきたりに沿って設定・活用することができる。が、起動時からとりあえず全開で出力されてしまう。コンソールにログイン後改めて設定すればその後は問題ないが、それまでの間止めておくことはできるんだろうか。
