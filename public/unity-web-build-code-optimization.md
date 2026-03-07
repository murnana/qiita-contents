---
title: '[Unity] Webビルド時の設定「Code Optimization」について'
tags:
  - Unity
private: false
updated_at: '2026-03-07T09:36:22+09:00'
id: 5c694b8e1aca39632407
organization_url_name: null
slide: false
ignorePublish: false
---

UnityでWebブラウザー向けのビルドを行う際、「Code Optimization」という項目があります。
これは、ビルド時の最適化において、何を重要視するのか、という設定になります。

- Unityバージョン: 6.3 LTS (6000.3.10f1)

## 各種ビルドのモード

### Shorter Build Time
ビルド時間が最も短い方法です。主に最適化はいいからとにかく早く成果物を見たいときに使用します。  
これがデフォルトの設定のため、何もしていないと大抵はこのモードです。

### Runtime Speed
実行時のパフォーマンスを重視したビルドになります。ビルド時間は長いです。

### Runtime Speed with LTO
実行時のパフォーマンスを重視しつつ、追加でLTOを有効にします。LTOについては後で説明します。

### Disk Size
ビルドサイズをとにかく小さくします。ビルド時間は長いです。
小さくなればWebページの起動時間が短縮されるため、場合によってはこちらを選択します。

### Disk Size with LTO
ビルドサイズをとにかく小さくしつつ、追加でLTOを有効にします。LTOについては後で説明します。


## LTO (Link Time Optimization) について
日本語では「リンク時最適化」と翻訳されることが多い最適化です。
この最適化について正しく理解するには、まずUnityがどのようにしてビルドを行っているのかを理解する必要があります。

Unityは以下の順でビルドします。
1. C#スクリプトをDLLへビルド (Roslyn C# コンパイラー)
2. DLLから、未使用のコードを削除 (Unity linkerによるManaged code stripping)
3. DLL内にあるILから、C++ソースコードへ変換 (IL2CPP コンパイラー)
4. C++ソースコードから、WebAssemblyバイナリファイルへ (Emscripten)

ここでカギとなるのが、C++ソースコードからWebAssemblyバイナリファイルへ変換するところです。

### LTOを理解するために - WebAssemblyバイナリファイルの生成手順

WebAssemblyバイナリファイルは、一般的な略称として「Wasm」(ワズム、と読む)が使われています。
そのため、以降は「Wasm」と略すことにします。

Wasmのビルドは2つの工程を踏みます。

まずC++ソースコードから、機械語へ変換します。この工程は「コンパイル」と呼ばれます。[^wasm-compile]

次に、機械語から実行可能なバイナリファイルへと変換されます。
この変換は、コンパイルされた命令群を集める作業になります。この工程を「リンク」と呼びます。

LTOの最適化は、まさにこの「リンク」に関する最適化になります。

### LTOを有効にするとどうなるのか
UnityはC++ソースコードからWebAssemblyバイナリファイルへ変換する際、ツールとして「Emscripten」を使用しています。
そのため、詳しく知りたい場合はEmscriptenについて調べるとよいです。

筆者もあまり詳しくないため、ここでは簡易的な説明だけに留めます。

LTOを有効にすると、コンパイル時点で即座に機械語へ変換するのではなく、中間表現（IR）のままにしておきます。
リンク時にプログラム全体のIRを一度に読み込み、まとめて最適化してから機械語を生成します。

LTOがない場合はシンボル情報のみを用いてリンクするため、ビルド時間は短い反面、モジュールをまたいだ最適化は行われません。


## 参考
- [Unity - Manual: Web build settings reference](https://docs.unity3d.com/6000.3/Documentation/Manual/web-build-settings.html)
- [Unity - Scripting API: WasmCodeOptimization](https://docs.unity3d.com/6000.3/Documentation/ScriptReference/WebGL.WasmCodeOptimization.html)
- [Unity - Manual: Web native plug-ins for Emscripten](https://docs.unity3d.com/Manual/webgl-native-plugins-with-emscripten.html)
- [Unity - Manual: Introduction to IL2CPP](https://docs.unity3d.com/Manual/il2cpp-introduction.html)
- [Unity - Manual: Managed code stripping](https://docs.unity3d.com/Manual/managed-code-stripping.html)

[^wasm-compile]: 厳密には、EmscriptenはC++をLLVM IR（中間表現）に変換します。「機械語」はここでは簡略化した表現です。
