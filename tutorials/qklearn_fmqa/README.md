# qklearn / SWIFT-FMQA / Qudas チュートリアル（MVTec AD 異常検知）

[English](README_en.md) | 日本語

MVTec ADの`tile`カテゴリを題材に、次の3つのライブラリを組み合わせて
画像の異常検知とハイパーパラメータ探索を体験するハンズオン用Notebookです。

- **qklearn**（Qukit-learn）: クラスタリングをQUBOとして定式化し、Amplifyで求解します
- **Qudas**: QUBOをdimodやAmplify Polyなど別の形式へ変換します
- **SWIFT-FMQA**（`amplify_bbopt`）: Factorization Machineを代理モデルとするブラックボックス最適化で、
  ハイパーパラメータを探索します

本チュートリアルでは量子実機は使用しません。QUBOの求解にはFixstars Amplifyを使います。

## ファイル構成

| ファイル | 内容 |
|---|---|
| `clustering_qklearn_fmqa_tutorial.ipynb` | ハンズオン用Notebook（上から順に実行します） |
| `clustering_qklearn_fmqa_tutorial_en.ipynb` | 上のNotebookの英語版（コードと実行結果は同一） |
| `README_en.md` | このREADMEの英語版 |
| `manifests/candidates/tile_manifest.csv` | 使用する画像と、学習・閾値決定・Validation・Testへの割り当て（画像そのものは含みません） |
| `requirements-lock.txt` | バージョンを固定した依存パッケージ一覧 |

## 必要なもの

1. **Python 3.11**（動作確認はこのバージョンのみです）
2. **Fixstars AmplifyのAPIトークン**
   無料トークンを https://amplify.fixstars.com/register で取得し、環境変数`AMPLIFY_TOKEN`に設定します。
   トークンはNotebookやファイルに書き込まないでください。
3. **MVTec AD（`tile`カテゴリ）**
   **このリポジトリには含まれていません。** 下記の手順で各自ダウンロードしてください。
4. GitHub（`github.com`）へアクセスできるネットワーク
   （`qklearn`と`amplify-bbopt-ext`をGitHubから、`pip install`でインストールするため）

## セットアップ

```bash
# 1. 仮想環境を作成（Python 3.11）
python3.11 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate

# 2. 依存パッケージをインストール
pip install -r requirements-lock.txt

# 3. Jupyterカーネルを登録（任意の名前でかまいません）
python -m ipykernel install --user --name qklearn_fmqa --display-name "Python 3.11 (qklearn_fmqa)"

# 4. Amplifyのトークンを設定（値は自分のトークンに置き換えてください）
export AMPLIFY_TOKEN="..."         # Windows PowerShell: $env:AMPLIFY_TOKEN = "..."
```

## MVTec ADの準備

MVTec ADは **CC BY-NC-SA 4.0** ライセンスです。**非商用の研究・教育目的に限って利用でき、再配布はできません。**
利用条件を確認したうえで、各自でダウンロードしてください。

1. 公式ページからデータセットをダウンロードします。
   https://www.mvtec.com/company/research/datasets/mvtec-ad
2. このフォルダに`data/MVTecAD/`を作り、アーカイブを展開します。
   このチュートリアルで使うのは`tile`カテゴリだけです。
3. 次のような構成になっていれば準備完了です。

```text
data/MVTecAD/tile/
├─ train/good/
├─ test/{good,crack,glue_strip,gray_stroke,oil,rough}/
└─ ground_truth/{crack,glue_strip,gray_stroke,oil,rough}/
```

別の場所に置く場合は、環境変数`MVTEC_ROOT`に`tile`フォルダの1つ上のフォルダを指定します。
`data/MVTecAD`があれば、そちらが優先されます。

## 実行方法

1. このフォルダでJupyterを起動します（`jupyter lab`など）。
   カレントディレクトリがNotebookのあるフォルダになるようにしてください。
2. `clustering_qklearn_fmqa_tutorial.ipynb`を開き、
   登録したカーネル（上の例では`Python 3.11 (qklearn_fmqa)`）を選びます。
3. 上から順にセルを実行します。

特徴量（MobileNetV2）は初回に計算され、`cache/`に保存されます（2回目以降は再利用されます）。
Amplifyのクラウドを数十回呼び出すため、全体で数分かかります。
Amplifyの求解には時間制限があり、実行のたびに数値が少し変わることがあります。

## ABCI-Qで実行する場合

Amplify AEの設定が異なります。第4章のコードのコメントに手順を記載しています。
ABCI-Qの公式ドキュメント: https://g-quat-abciq.github.io/abciq-docs/ja/apps/amplify-ae/

## ライセンスとデータの扱い

- このリポジトリのコードは、リポジトリ直下の`LICENSE`に従います。
- MVTec ADは別ライセンス（CC BY-NC-SA 4.0）です。データはこのリポジトリに含めず、
  ユーザー自身が公式ページから取得してください。
- `tile_manifest.csv`には、画像のファイル名・ハッシュ値・割り当てを記載しています（画像データは含みません）。
