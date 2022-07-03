---
layout: default
title:  "Raspberry Piでshairport-syncを使う"
date:   2022-07-03 19:30:00 +0900
categories: make raspberrypi
---

# Raspberry Piでshairport-syncを使う

## Raspberry Pi OS Lite(＝デスクトップ環境がない)の場合

shairport-syncの初期状態は、iPhone等から受け取った信号をALSAに流し込む設定になっているので、aptでインストールしたら基本的にそのまま使える。

## Raspberry Pi OS(＝デスクトップ環境がある)の場合

PulseAudioがデスクトップ環境の音声まわりを管理しているため、shairport-syncのインストール直後の状態では音が出ない。shairport-syncをPulseAudio出力の設定にすると、セキュリティのためsystemdでshairport-syncを起動できない。一般ユーザーで起動する必要がある。現状、しぶしぶ.bashrcに下記スクリプトを追加して、起動時に1回だけ実行される(ssh等でログインしたときに重ねて実行されない)ようにしている。

```
ss=$(pgrep shairport-sync)
if [ -z "$ss" ]; then
  /usr/bin/shairport-sync &
fi
```
