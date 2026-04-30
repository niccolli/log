---
layout: default
title:  "Raspberry PiでM8 Headlessを扱う基盤をととのえる"
date:   2026-04-30 08:00:00 +0900
categories: make raspberrypi
---

# Raspberry PiでM8 Headlessを扱う基盤をととのえる

Raspberry Piを使ってM8 Headlessを動かす手順をまとめます。基本的には[m8cのREADME](https://github.com/laamaa/m8c)で十分なのですが、細かいつまずきポイントなどを追記しています。

## 準備

### Teensy 4.1にM8 Headlessのファームウェアを書き込む

macOSの場合、ファームウェア書き込みに使う[TyTools](https://github.com/Koromix/tytools)が古く、TyUploaderは起動してもファイル選択ができません。Issueに、[tycmdを使いターミナルから書き込む解決策](https://github.com/Koromix/tytools/issues/117#issuecomment-3938423202)があります。ただし、tycmdもセキュリティ機能の制限によりブロックされます。一度Finderから起動させ、設定.app→プライバシーとセキュリティ→下のほうの実行許可で起動するようになります。

### Raspberry PiにOSを書き込む

今回は下記環境で試しています。

- Raspberry Pi 4 8GB
- Raspberry Pi OS 2026-04-21版 (標準/Liteどちらでも)

Raspberry Pi 4はメモリが少ないものでもいけるかもしれません。Raspberry Pi Zero 2でもできるか試しています。

一番新しいRaspberry Pi OSでも大丈夫でした。なぜかLiteだけ無線LANが無効になっています。rfkillコマンドで解除できますが、設定が済めば不要なので有線LANで対処しました。

## m8cのビルドから起動まで

m8cの[Building from source code](https://github.com/laamaa/m8c#building-from-source-code)の章に沿って実行します。Raspberry Piでもそのままの手順で動きます。

### デスクトップ環境以外から起動する

デスクトップ版のRaspberry Pi OSにSSHなどでログインしている場合、画面がないと言われてアプリが止まります。下記のように環境変数を設定すればOKです。

```
$ DISPLAY=:0 ./m8c
```

Lite版ではディスプレイ設定の必要なく、m8cを実行するだけでそのまま表示されます。

###　マウスポインターを消したい

m8c起動後にFullscreen設定をOnにするとマウスポインターが消えますが、起動時に設定ファイルでフルスクリーンを有効にしていても反映されません。常時フルスクリーン稼働の場合はマウスポインターは表示されなくていいので、下記のようにコードを修正してからビルド・実行します。

```
# src/render.c
void render_screen(config_params_s *conf) {
  # ここを追加
  SDL_HideCursor();
  if (!dirty && !settings_is_open()) {
```

### イヤホン端子ではなくDACを指定する

PCM5122など、広く使われているDACを指定するときは、下記手順でOKです。

1. Raspberry Piの /boot/firmware/config.txt に適切に設定する。
1. alsamixerに表示されることを確認し、表示名を記録する。
1. m8cの設定ファイルの audio_device_name の項に、alsamixerの表示名と同じものを指定する。

### Raspberry Piの起動後、すぐにm8cを実行させる。

crontabへの指定で十分です([参考](https://qiita.com/karaage0703/items/ed18f318a1775b28eab4#crontab-reboot))。@reboot としてm8cのフルパスを指定すれば、起動時にフルスクリーンで表示されます。

## 実行例

上記手順で設定したうえで実行すると、下記のようになります。

![](https://raw.githubusercontent.com/niccolli/log/main/images/20260430.jpg)

液晶画面出力は[過去の記事](https://niccol.li/log/make/raspberrypi/2024/05/21/Raspberry_Pi_ili9341.html)を参照ください。
