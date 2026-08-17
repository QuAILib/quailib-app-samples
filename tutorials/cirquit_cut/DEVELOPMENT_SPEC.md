# 物流配送先割当・量子回路分割チュートリアル開発仕様

## 1. 目的

既存の次のNotebookを保存したまま、Qudas、Qiskit、および現在のQuantumCircuitCutting系ライブラリを用いた、技術的に正確で再現可能な改良版チュートリアルを開発する。

- 元Notebook（変更禁止）  
  `tutorials/cirquit_cut/transportation_curcuit_cut_tutorial.ipynb`
- 最終成果物  
  `tutorials/cirquit_cut/transportation_circuit_cut_tutorial_development.ipynb`

既存フォルダ名`cirquit_cut`と元Notebook名`curcuit`にはスペルミスがあるが、既存パスは互換性のため変更しない。新しいNotebook名・Pythonモジュール名では正しい`circuit`を使用する。

現在の元Notebookは151セル（Markdown 84、Code 67）で、以下の流れを持つ。

1. 配送先座標と距離・関係行列
2. QUBO作成
3. Qudasによるdimod変換とNeal求解
4. 小規模問題用のQAOA風回路
5. カット候補探索
6. ワイヤカット
7. 部分回路展開
8. 一部回路の実行
9. 最頻ビット列と多数決による簡易結合

改良版では、説明・可視化の良い部分を残しつつ、後半を正式な量子回路分割検証へ置き換える。

---

## 2. 今回の主要な技術課題

### 2.1 物流問題の意味

現行のMax-Cut型目的関数は、距離が近く関係が強い配送先を別チームへ分ける方向に働く。一般的な地理的配送エリア分割としては直感と逆である。

本編では原則として、次の均衡付き地理的分割を採用する。

- 近い配送先が別チームになることをペナルティ化
- 両チームの件数を均衡させる
- 目的関数と業務上の意味を明示する

数式例：

\[
E(x)
=
\sum_{i<j} w_{ij}(x_i-x_j)^2
+
\lambda\left(\sum_i x_i-\frac{n}{2}\right)^2
\]

ただし、Gate Bで現行Max-Cut型を維持する合理的理由が確認された場合は、問題名を「関係の強い配送先を分散させる負荷分散型割当」に修正し、地理的クラスタリングとは呼ばない。どちらを採用したかを設計判断として記録する。

### 2.2 同一問題インスタンス

前半8配送先、後半4配送先を別乱数で生成する構成は禁止する。

本編のend-to-end検証では、明示的に固定した4配送先をQUBO、厳密解、QAOA、直接実行、回路分割、再構成まで一貫して使用する。

8配送先は任意の拡張例として扱ってよいが、同一結果比較へ混ぜない。

### 2.3 QUBO→Ising→量子回路

現在の回路は一部の相互作用しか含まず、QUBO係数を厳密なIsing変換なしで回転角へ利用している。

改良版では以下を必須とする。

- QUBOの行列規約を一つに固定
- `x^T Q x`の二重計数を防ぐ
- QUBOを定数項、単一Z項、ZZ項へ厳密変換
- 全ビット列でQUBOとIsingのエネルギーが定数差を除いて一致
- 全非ゼロ相互作用をコスト回路へ含める
- Qiskitのbit orderを明示してテストする

### 2.4 QAOAパラメータ

任意の`gamma=0.6`, `beta=0.4`を最終値として使用しない。

小規模問題について`.py`でパラメータ探索を行い、直接回路の期待コストまたは最適解確率に基づいて設定を凍結する。最終Notebookは探索を再実行しない。

### 2.5 カットAPI

カット位置探索APIの複合戻り値中の全整数をゲートIDとして収集してはならない。

Gate Aで現在使用するライブラリのsource commitとAPIを調査し、戻り値の意味を明示的に分解する。

ゲートカット候補探索とワイヤカットを独立に実行してつながらない構成は禁止する。本編では一つのカット方式を選び、選定したカット位置を実際の分割へ使用する。

### 2.6 全bundleの実行

展開されたbundleの最初の1件だけを実行してはならない。

正式再構成に必要な全bundle、係数、境界条件、測定基底を列挙し、全て実行する。

### 2.7 正式な再構成

以下は正式再構成として扱わない。

- 各部分回路の最頻ビット列だけを採用
- 重複量子ビットを多数決
- 部分ビット列の単純連結
- 不足データを0で補完

現在の簡易再構成にはQubitオブジェクトと整数キーの不一致により`0000`が生成されるバグがあるため、当該ロジックは削除する。

現在のライブラリが正式な確率分布または期待値の再構成APIを提供する場合は、それを使用する。

提供しない場合は勝手に別ライブラリへ置換せず、次を報告して停止する。

- 現行ライブラリで可能な範囲
- 欠けている再構成機能
- 部分回路生成までに限定する案
- 別ライブラリを採用する案

ユーザー承認後に方針を変更する。

---

## 3. 対象範囲

変更対象：

- `tutorials/cirquit_cut/**`
- 必要最小限のルート`.gitignore`
- `.claude/settings.json`への元Notebook保護ルール追加

変更禁止：

- `tutorials/qklearn_fmqa/**`
- `tutorials/cirquit_cut/transportation_curcuit_cut_tutorial.ipynb`
- `LICENSE`

ユーザーの明示指示まで禁止：

- `git commit`
- `git push`
- destructive Git operations
- システムソフトウェアの自動インストール
- 別の回路カットライブラリへの無断変更

---

## 4. 開発方式

### 4.1 Pythonコードが実装本体

以下はすべて`.py`で実施する。

- 問題データ作成
- QUBO作成
- 全探索
- Qudas変換
- Neal求解
- QUBO→Ising変換
- QAOAパラメータ探索
- 直接回路実行
- カット位置探索
- circuit adapter
- fragment / bundle生成
- 全bundle実行
- 正式再構成
- 誤差評価
- shot sensitivity
- resource analysis
- レポート生成
- Notebook生成

### 4.2 Notebookの役割

Notebookは完成済みモジュールと凍結済み結果を使う教材とする。

Notebookで禁止：

- パラメータ探索
- カット候補の反復探索
- 全bundleの開発用再実行
- private API調査
- 場当たり的な再構成
- ライブラリインストール
- 長い生ログ
- オブジェクトアドレスの出力

---

## 5. Python環境

qklearn用venvは使用しない。

以下の専用環境変数を使用する。

- `CIRCUIT_CUT_VENV`
- `CIRCUIT_CUT_PYTHON`
- `CIRCUIT_CUT_CACHE`
- `UV_SYSTEM_CERTS=true`
- `PYTHONNOUSERSITE=1`
- `PIP_REQUIRE_VIRTUALENV=true`

既定の専用venv候補：

`C:\Users\E1804\work\venvs\circuit-cut-py311`

Gate Aで各ライブラリのPython対応を確認する。Python 3.11が不適合な場合は、勝手に別環境を作らず、必要バージョンと理由を報告する。

依存関係は可能な限りバージョンまたはcommit SHAで固定する。

---

## 6. 推奨フォルダ構成

```text
tutorials/cirquit_cut/
├─ CLAUDE.md
├─ DEVELOPMENT_SPEC.md
├─ CLAUDE_CODE_PROMPTS.md
├─ README.md
├─ transportation_curcuit_cut_tutorial.ipynb
├─ transportation_circuit_cut_tutorial_development.ipynb
├─ requirements.in
├─ requirements-lock.txt
├─ configs/
│  ├─ default.yaml
│  ├─ problem_4nodes.yaml
│  ├─ problem_8nodes.yaml
│  ├─ pinned_qaoa_parameters.yaml
│  ├─ cut_plan.yaml
│  └─ source_versions.json
├─ circuit_cut_app/
│  ├─ __init__.py
│  ├─ paths.py
│  ├─ config.py
│  ├─ problem.py
│  ├─ qubo.py
│  ├─ exact_solver.py
│  ├─ qudas_bridge.py
│  ├─ ising.py
│  ├─ bit_order.py
│  ├─ qaoa.py
│  ├─ parameter_search.py
│  ├─ cut_locator.py
│  ├─ circuit_adapter.py
│  ├─ fragment_generator.py
│  ├─ executor.py
│  ├─ reconstruction.py
│  ├─ metrics.py
│  ├─ reporting.py
│  ├─ notebook_builder.py
│  └─ cli/
│     ├─ verify_environment.py
│     ├─ build_problem.py
│     ├─ run_classical_validation.py
│     ├─ run_parameter_search.py
│     ├─ run_direct_circuit.py
│     ├─ analyze_cut_candidates.py
│     ├─ generate_fragments.py
│     ├─ run_fragments.py
│     ├─ reconstruct_results.py
│     ├─ run_resource_analysis.py
│     ├─ build_final_notebook.py
│     └─ execute_final_notebook.py
├─ scripts/
│  ├─ setup_venv.ps1
│  ├─ activate_env.ps1
│  ├─ run_in_env.ps1
│  ├─ start_jupyter.ps1
│  └─ verify_all.ps1
├─ reports/
│  ├─ api_capability_report.md
│  ├─ classical_validation.json
│  ├─ qaoa_parameter_search.csv
│  ├─ direct_circuit_results.json
│  ├─ cut_plan_report.json
│  ├─ fragment_manifest.json
│  ├─ reconstruction_results.json
│  ├─ resource_analysis.json
│  ├─ figures/
│  └─ DEVELOPMENT_REPORT.md
└─ tests/
   ├─ test_original_notebook_unchanged.py
   ├─ test_environment.py
   ├─ test_problem_definition.py
   ├─ test_qubo_energy.py
   ├─ test_qudas_conversion.py
   ├─ test_bit_order.py
   ├─ test_ising_conversion.py
   ├─ test_qaoa_cost_circuit.py
   ├─ test_cut_locator_contract.py
   ├─ test_selected_cut_is_applied.py
   ├─ test_fragment_completeness.py
   ├─ test_reconstruction.py
   ├─ test_no_heuristic_reconstruction.py
   └─ test_notebook_contract.py
```

---

## 7. Gate A：環境・API能力・元Notebook保護

実施内容：

1. 元NotebookのSHA-256とGit blob hashを一度だけ記録
2. 元Notebookの非変更テスト
3. `.claude/settings.json`へ元NotebookのEdit denyを追加
4. 専用venvの検証または作成
5. Notebookのimportとmetadataを解析
6. Qudas、Qiskit、Aer、QuantumCircuitCutting系3モジュールの実体を特定
7. package versionまたはsource commit SHAを固定
8. public API、内部API、cut locator戻り値構造を調査
9. formal reconstruction APIの有無を調査
10. `reports/api_capability_report.md`を作成
11. import smoke test、pip check、pytest

Gate A停止条件：

- 使用ライブラリの取得元を特定できない
- Python互換性がない
- formal reconstructionの実装可能性を判断できない
- システム依存がユーザー対応を必要とする

Gate Aでは問題ロジックをまだ変更しない。

受入条件：

- 元Notebook不変
- 専用Python環境
- 全主要import成功
- API能力レポート完成
- private API利用箇所が特定済み
- formal reconstructionへの実装経路が明確、または不足を明確に報告

---

## 8. Gate B：決定論的問題・QUBO・厳密古典検証

実施内容：

1. 明示的な固定4配送先データを作成
2. 任意で8配送先拡張データ
3. 物流目的関数を確定
4. QUBO規約を確定
5. QUBOを作成
6. 全ビット列を列挙して厳密解を取得
7. チームラベル全反転の対称性を考慮
8. Qudasでdimodへ変換
9. Nealで求解
10. QUBO直接エネルギー、dimodエネルギー、Neal最良値を比較
11. 配送先・関係グラフ・QUBOヒートマップを保存

受入条件：

- 全ビット列でQUBO energyと定義済みobjectiveが一致
- Qudas変換後のenergy一致
- Nealが厳密最適値へ到達
- 同一問題インスタンスを後続Gateで使用
- 乱数の消費順に依存しない

---

## 9. Gate C：QUBO→Ising・QAOA直接回路

実施内容：

1. QUBO→Ising変換
2. 全基底状態でenergy一致
3. 全非ゼロh/ZおよびJ/ZZ項を回路へ実装
4. bit orderを一つに固定
5. p=1を基本としたQAOA回路を構築
6. `.py`でgamma/beta探索
7. 最良パラメータを凍結
8. 分割前回路をstatevectorとshot-basedで直接実行
9. 最適解確率、期待QUBO energy、分布を保存
10. 厳密最適解との比較

受入条件：

- QUBO/Ising energy一致
- 相互作用欠落なし
- bit orderテスト成功
- 凍結パラメータの出所が明確
- 直接実行結果が保存済み

QAOAが厳密最適解を高確率で出さない場合も、その結果を隠さない。

---

## 10. Gate D：カット位置・全fragment生成

実施内容：

1. Gate Aで確認した正式APIを使用
2. 本編のcut方式を一つに決定
3. locator戻り値を型・意味ごとに明示分解
4. 選定したcutが実際の分割へ使われることをテスト
5. fragmentを生成
6. 展開に必要な全bundleを生成
7. bundle係数、境界条件、測定設定をmanifest化
8. 最大fragment量子ビット数、深さ、2量子ビットゲート数を計測
9. 元回路とfragmentを可視化
10. private APIが必要なら`circuit_adapter.py`へ隔離

受入条件：

- 選定cutと適用cutが一致
- 全bundleが列挙
- 最初のbundleだけに限定しない
- 最大fragment量子ビット数が元回路より小さい
- fragment manifestが再現可能
- Notebookにprivate API処理を置かない

---

## 11. Gate E：全fragment実行・正式再構成

前提：

Gate Aでformal reconstruction pathが確認済みであること。

実施内容：

1. 分割前回路の直接実行結果を固定
2. 必要な全fragment/bundleを実行
3. 全係数・境界条件を使用
4. 公式または検証済みの再構成式を使用
5. 期待値または確率分布を再構成
6. 分割前結果と比較
7. shot数を変えた感度分析
8. 再構成時間・回路数・総shotsを記録

禁止：

- 最頻ビット列だけの採用
- 多数決
- 単純連結
- 欠落ビットの0補完
- 一部bundleだけの使用

評価指標：

確率分布が再構成可能な場合：

- Total Variation Distance
- Hellinger fidelity
- 最頻ビット列一致
- QUBO期待値差

期待値のみの場合：

- cost expectation absolute error
- 各Pauli expectation error
- shot-based confidence interval

受入条件：

- formal reconstructionが完了
- 直接実行との誤差を数値報告
- 必要な全bundleが実行済み
- heuristic reconstructionが存在しない
- team-label symmetryを考慮
- 失敗時は結果を捏造せず停止

---

## 12. Gate F：リソース分析・結果凍結

実施内容：

1. 直接回路とfragmentの量子ビット数・深さ・2量子ビットゲート数比較
2. 実行回路数
3. 総shots
4. reconstruction overhead
5. shot数と誤差の関係
6. 結果をJSON/CSVへ凍結
7. `DEVELOPMENT_REPORT.md`作成

主張：

- circuit cuttingによる最大回路幅削減
- その代償としての回路数・shots・再構成コスト増加
- simulator上の検証であり、実機優位性の証明ではない

---

## 13. Gate G：最終Notebook生成・全体検証

最終NotebookはGate F完了後に初めて生成する。

目安：

- 55～70セル
- 日本語中心
- qklearn最終版と同様の表・見出し・注意書きのトーン
- 生の開発ログを出さない
- 主要可視化を十分に含める

推奨章：

1. 目的
2. 各ライブラリの役割
3. 問題設定
4. 配送先可視化
5. 物流目的関数
6. QUBO
7. 厳密解
8. Qudas変換
9. Neal比較
10. Ising変換
11. energy一致
12. QAOA回路
13. パラメータ探索結果
14. 分割前直接実行
15. cut位置探索
16. cut位置可視化
17. fragment生成
18. fragment規模
19. 全bundle
20. 正式再構成
21. 分割前後比較
22. shot sensitivity
23. resource/overhead
24. 配送問題としての解釈
25. 制約・限界
26. 再実行方法

可視化：

- 配送先・関係グラフ
- distance/weight行列
- QUBOヒートマップ
- 最適割当
- QAOAパラメータ面
- 直接実行分布
- cut位置付き回路
- fragment回路
- 分割前後分布または期待値
- 誤差指標
- resource比較
- shot数対誤差

Notebook契約：

- 全セル実行成功
- 探索を再実行しない
- 一部bundleだけを使わない
- heuristic reconstructionなし
- 元Notebook変更なし
- local absolute pathなし
- secretなし
- private APIは互換層の呼び出しだけ
- frozen resultとNotebook表示が一致

---

## 14. テスト必須項目

- 元Notebook hash
- QUBO objective一致
- Qudas energy一致
- exact optimum一致
- bit inversion symmetry
- QUBO→Ising一致
- bit order
- 全相互作用が回路へ含まれる
- cut locator API contract
- selected cut applied
- all bundles present
- reconstruction coefficients complete
- direct/reconstructed metric
- no heuristic reconstruction code
- no `Qubit`/int key mismatch
- notebook contract

---

## 15. 停止条件

以下で停止して報告する。

- 現行ライブラリの取得元やversionを特定できない
- formal reconstruction APIまたは数式を確認できない
- public APIでは必要処理を実装できない
- Python互換性不整合
- ユーザーによるシステム対応が必要
- 直接回路と再構成結果が合理的に一致しない
- 必要な全bundleを生成できない
- ライブラリ変更の承認が必要

停止時に必ず残す。

- 完了Gate
- 失敗箇所
- 実行済みテスト
- 技術的根拠
- 選択可能な次案
- 再開方法

---

## 16. 受入条件

### 環境

- 専用venv
- 依存固定
- pip check成功
- API能力レポート

### 数理

- 決定論的問題
- QUBO・objective一致
- 厳密解
- Qudas/Neal一致

### 量子回路

- Ising一致
- 全相互作用
- bit order正確
- direct baselineあり

### 回路分割

- 選定cutを使用
- 全bundle
- 正式再構成
- 直接結果との数値比較
- resource/overhead報告

### Notebook

- 元Notebook不変
- 開発版別ファイル
- 全セル成功
- 説明・可視化十分
- heuristic resultを正式結果と表現しない
- qklearn版とトーン統一
