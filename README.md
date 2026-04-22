# 量子・AIハイブリッド技術を活用したアプリケーション開発チュートリアル

このリポジトリでは、量子コンピューティングとAIを組み合わせた技術を活用し、アプリケーションの開発・検証を行うためのチュートリアルを公開しています。量子・AIハイブリッド技術の活用方法を具体的なサンプルを通じて実践的に学ぶことができ、産総研が整備する量子・古典計算基盤（ABCI-Q）上で実行・評価することができます。

# チュートリアルの内容

本チュートリアルは、アプリケーション開発者や研究者が、量子・AIハイブリッド技術を活用した開発の基本を理解できるようJupyter Notebook形式で提供されます。具体的なサンプルコードを通じて、実際の開発・検証の流れを体験できる構成となっています。
チュートリアルでは、以下の量子・AIハイブリッド共通ライブラリを取り上げています。
1.	SWIFT-FMQA（ブラックボックス最適化）
   ブラックボックス最適化手法であるFMQA (Factorization Machine with Quadratic-optimization Annealing)を拡張した手法で、複雑な最適化問題への適用が可能なライブラリです[1]。慶應義塾大学田中研究室により開発されました。
   https://github.com/Shu-Tanaka-Group/amplify-bbopt-ext
2.	Qukit-learn（クラスタリング）
   Scikit-learn互換のインタフェースを備え、量子機械学習を用いたクラスタリング手法を量子・AIハイブリッド環境で実装するライブラリです。東北大学小松研究室により開発されました。
   https://github.com/kazukomatsu/qukit-learn-nedo
3.	QuantumCircuitCutting（量子回路分割）
   大規模な量子回路を分割し、小規模な量子コンピュータでも実行可能とするためのライブラリです。PwCコンサルティング合同会社により開発されました。
   https://github.com/QuantumCircuitCutting  

本チュートリアルでは、これらの共通ライブラリを活用した具体的なアプリケーションの作成のための応用的なチュートリアルとなっています。各共通ライブラリの基本的な使用方法は、それぞれのサイトに掲載されています。
さらに本チュートリアルでは、Qudasと呼ばれる入出力変換ライブラリ（本事業にて開発）を活用しており、異なる量子／AIフレームワークの異なるデータ形式を統一的に扱う方法を学ぶことができます。これにより、複数のソルバー間での結果比較やベンチマーキングへの活用が可能となります。


## フォルダ構成

```text
quailib-app-samples/
├─ README.md
├─ LICENSE
├─ .gitignore
├─ tutorials/
│  ├─ curquit_cut/
│  │  └─ transportation_circuit_cut_tutorial.ipynb
│  └─ qklearn_fmqa/
│     └─ clustering_qklearn_fmqa_tutorial.ipynb
└─ docs/
```

## 使い方
チュートリアルはJupyter Notebook形式で提供されます。それぞれのノートブックをローカル環境やABCI-Q Open OnDemand (https://g-quat-abciq.github.io/abciq-docs/ja/open-ondemand/) 機能で実行することができます。


## 謝辞
本成果は、国立研究開発法人新エネルギー・産業技術総合開発機構 (ＮＥＤＯ) の助成事業として得られたものです。