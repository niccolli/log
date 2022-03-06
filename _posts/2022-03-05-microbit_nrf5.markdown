---
layout: default
title:  "micro:bitでnRF5 SDKを動かす"
date:   2022-03-06 23:30:00 +0900
categories: make
---

# micro:bitでnRF5 SDKを動かす

[micro:bit v2](https://microbit.org/ja/)は、メインのマイコンとしてNordic nRF52833が載っています。micro:bitの基板上には、デバッグインタフェースとしてもう一つのマイコン(KL27Z)が載っており、ちょっとした手順を踏むことでnRF52833の開発基板として使うことができます。私が試したときの記録を、忘れないように残しておきます。

## KL27ZをJ-LINKに置き換える

SEGGERから、[KL27Zの機能をJ-LINKに置き換えるファームウェア](https://www.segger.com/products/debug-probes/j-link/models/other-j-links/bbc-microbit-j-link-upgrade/)が公開されています。リンク先の手順にしたがってファームウェアを書き換えることでKL27ZがJ-LINK相当になり、micro:bitをUSB接続するだけでIDEからデバッグインタフェース経由でnRF52833が認識されるようになります。

## SEGGER Embedded StudioでnRF5 SDKのサンプルをビルドして、micro:bitで動作させる

今回、nRF52833で対応しているZigbeeを触ってみようと思い、[nRF5 SDK for Thread and Zigbee](https://www.nordicsemi.com/Products/Development-software/nRF5-SDK-for-Thread-and-Zigbee/)を使いました。IDEには、Nordicには無償で利用できるSEGGER Embedded Studioを使います。

1. v4.1.0(記載時の最新版)をダウンロード
1. Zipファイルを解凍
1. 何らかのサンプルを選び、 pca10100/blank/ses/(プロジェクト名).emProject をEmbedded Studioで開く。
1. sdk_config.hとpca10100.hをmicro:bitに合わせて変更する。
1. メニューからビルドして実行。

pca10100は、Nordicの開発ボードnRF52833 DKを指します。これをベースに、micro:bit向けに設定ファイルを調整すると、micro:bit上でサンプルコードを動作させることができるようになります。

### ヘッダファイルの変更箇所

- nrf5sdkforthreadv41/examples/(サンプルのパス)/pca10100/blank/config/sdk_config.h
  - #define NRFX_CLOCK_CONFIG_LF_SRC 1 → 0
  - #define CLOCK_CONFIG_LF_SRC 1 → 0
- nrf5sdkforthreadv41/components/boards/pca10100.h
  - #define RX_PIN_NUMBER  8 → NRF_GPIO_PIN_MAP(1,8)
  - #define TX_PIN_NUMBER  6 → NRF_GPIO_PIN_MAP(0,6)
  - #define HWFC           true → false

sdk_config.hは32kHzの発振子がないことの対応を、pca10100.hはUART(とボタン・LED)のピン割り当ての対応を行います。UARTはサンプルアプリによって使うdefine文が違うので、必要に応じて他の設定も書き換えます。

nRF52833から、GPIOのポートが増えています。数字だけの指定ならばポート0とみなされ、ポート1のGPIOを指定する場合は、上記のようにマクロを介して記載します。micro:bitのKL27ZがもつUSB-UART変換を使う場合、上記のように置き換えてください。
