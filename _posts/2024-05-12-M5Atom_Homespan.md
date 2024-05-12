---
layout: default
title:  "HomespanでM5AtomをHomeKitデバイスにする"
date:   2024-05-12 22:00:00 +0900
categories: make esp32 homekit
---

# HomespanでM5AtomをHomeKitデバイスにする

[Homespan](https://github.com/HomeSpan/HomeSpan)という、ESP32をHomeKitデバイスとして動かすためのArduino IDEライブラリがあります。もちろんM5シリーズにも対応しているので、iPhoneのホームアプリをUIとしてM5シリーズのデバイスに指示を出すことができます。

## 使い方

基本的に[サンプルコード](https://github.com/HomeSpan/HomeSpan/tree/master/examples)が充実しており、コード記載例にコメントとしてみっちりと解説が記載されています。そのままビルドが通ってすぐ試せます。

無線LANの接続先は、コードに記載せず、実行時にシリアルポートから設定します。シリアルポートに接続してから起動orリセットすると、対話式インタフェースにより無線LANの接続先やiPhoneとのペアリングを設定します。

## 活用

ESP32には設定値が流れてきますが、受け取った値をどのように使うかはデバイス次第です。値を受け取ったあとモーターを回したりしてもiPhone側は特に気にしないので、好きなように活用するのもよいと思います(モーターを回すインタフェース自体は自動カーテン制御として含まれているようです)。

![](https://raw.githubusercontent.com/niccolli/log/main/images/20240512_1.jpg)

うちの場合は、M5ATOMの先にNeoPixel互換テープLEDを接続して、明るさ調整のインタフェースとして使っています。

![](https://raw.githubusercontent.com/niccolli/log/main/images/20240512_2.jpg)

薄暗くした部屋でジオラマに仕込むと、ちょうどいい明るさに設定するのが簡単になります。iPhone側のアプリ・UIを作り込む必要がないのが楽でいいですね。
