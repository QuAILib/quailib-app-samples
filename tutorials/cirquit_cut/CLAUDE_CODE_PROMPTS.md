# Claude Code 操作用の短い指示文

詳細条件は `DEVELOPMENT_SPEC.md` に集約している。  
同じリポジトリを使うが、qklearnとは別のClaude Codeセッション・別worktree・別venvで進める。

---

## 1. Planモード

```text
ルートのCLAUDE.mdと
tutorials/cirquit_cut/CLAUDE.mdと
tutorials/cirquit_cut/DEVELOPMENT_SPEC.md
を最初から最後まで読んでください。

このターンでは一切ファイルを変更せず、
元Notebook、現在のライブラリ利用方法、フォルダ構成を調査してください。

Gate A～Gに対応した実装計画、依存関係、API調査方法、技術的リスク、停止条件を提示してください。

特に次を明示してください。

- 元Notebookは変更しない
- qklearnチュートリアルへ触れない
- 専用venvを使用する
- 試行錯誤は.pyで行う
- 正式再構成の実装可能性をGate Aで先に確認する
- 最頻ビット列＋多数決の簡易再構成を廃止する
- formal reconstructionが不可能なら勝手に別ライブラリへ変更せず停止する
- 最終NotebookはGate F完了後に生成する
```

---

## 2. Gate A

```text
計画を承認します。

DEVELOPMENT_SPEC.mdのGate Aだけを実装してください。

環境・依存関係・API能力・元Notebook保護までで停止してください。
問題定義、QUBO改訂、量子回路生成、回路分割の実装にはまだ進まないでください。

特に、現在のQuantumCircuitCutting系ライブラリについて、
取得元、version/commit、public API、private API依存、cut locator戻り値構造、
全bundle生成経路、formal reconstruction APIの有無を調査し、
api_capability_report.mdへ記録してください。

formal reconstructionの実装経路が確認できない場合は、その時点で停止してください。
git commit / git pushは行わないでください。
```

---

## 3. Gate B

```text
Gate Aの結果を承認します。
DEVELOPMENT_SPEC.mdのGate Bだけを実装してください。

固定4配送先の同一問題を作り、
物流目的関数、QUBO、全探索、Qudas変換、Neal求解を.pyで検証してください。

QUBO energy、業務objective、dimod energy、厳密最適値の一致をテストしてください。
後半用に別乱数の問題を作らないでください。

Gate C以降、Notebook生成、git commit/pushには進まないでください。
```

---

## 4. Gate C

```text
DEVELOPMENT_SPEC.mdのGate Cだけを実装してください。

QUBO→Ising変換、全基底状態のenergy一致、全非ゼロ相互作用を含むQAOA回路、
bit order、パラメータ探索、分割前回路の直接実行まで行ってください。

探索は.pyで行い、最良パラメータと直接実行結果を保存してください。
回路分割にはまだ進まないでください。
```

---

## 5. Gate D

```text
DEVELOPMENT_SPEC.mdのGate Dだけを実装してください。

Gate Aで確認済みのAPIを用い、
cut位置の選定、選定cutの適用、fragment生成、全bundle列挙、
係数・境界条件・測定設定のmanifest化、resource測定まで行ってください。

最初のbundleだけを処理してはいけません。
private APIが必要ならcompatibility moduleへ隔離してください。
正式再構成とNotebook生成にはまだ進まないでください。
```

---

## 6. Gate E

```text
DEVELOPMENT_SPEC.mdのGate Eだけを実装してください。

分割前回路の固定結果と、必要な全fragment/bundleの実行結果を用い、
確認済みの正式な再構成方式だけで結果を再構成してください。

最頻ビット列、多数決、単純連結、0補完は禁止です。

直接結果との誤差、実行回路数、総shots、再構成時間を報告してください。

formal reconstructionが成立しない場合は結果を捏造せず停止してください。
```

---

## 7. Gate F～G

```text
Gate A～Eの完了を確認してください。

DEVELOPMENT_SPEC.mdのGate Fでresource/overhead分析と結果凍結を行い、
その後Gate Gで初めて
transportation_circuit_cut_tutorial_development.ipynb
をnotebook_builder.pyから生成してください。

Notebookは探索・cut開発・formal reconstruction開発を再実行せず、
安定化済みモジュールと凍結結果を使う教材としてください。

生成後、全セル実行、pytest、verify_all.ps1、元Notebook非変更、
secret/path/private-API contractを検証してください。

git commit / git pushはまだ行わないでください。
```
